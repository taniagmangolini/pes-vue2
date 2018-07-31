<template>
 <form method="POST" enctype="multipart/form-data">
  <div class="card">
    <div class="card-content">

        <div class="file has-name is-fullwidth" >
            <label class="file-label">
                <input class="file-input" type="file" @change="onFileChanged" >
                    <span class="file-cta">
                        <span class="file-icon">
                            <i class="fas fa-upload"></i>
                        </span>
                        <span class="file-label">
                            Choose a file…
                        </span>
                    </span>
                <span class="file-name">
                    {{fileName}}
                </span>
            </label>
        </div>

        <div class="field has-text-left">
            <label class="label">Taxon*</label>
            <div class="control has-text-centered">
               <div class="select is-fullwidth">
                            <select v-model="taxonId" name="taxonId"> 
                                <option value="3077">Chrorella</option>
                                <option value="3055">Chlamydomonas reinhardtii</option>
                                <option value="9606">Human</option>
                                <option value="10116">Rattus norvegicus</option>
                                <option value="10117">Rattus rattus</option>
                                <option value="4547">Saccharum officinarum</option>
                                <option value="4558">Sorghum bicolor</option>
                                <option value="4557">Zea mays</option>
                            </select>
               </div>
            </div>
        </div>
        <div class=" columns is-multiline">
        <div class="column is-half field has-text-left">
            <label class="label">Minimum Number Of Proteins*</label>
            <div class="control has-text-centered">
                <input class="input" type="text" name="minProteins" v-model="minProteins">
            </div>
        </div>  

        <div class="column is-half field has-text-left">
            <label class="label">Null Distributions*</label>
            <div class="control has-text-centered">
                <input class="input" type="text" name="nullDistributions" v-model="nullDistributions">
            </div>
        </div>  

    </div>
    
    <div class=" columns is-multiline">
        <div class="column is-half field has-text-left">
            <label class="label">Tolerance Factor*</label>
            <div class="control has-text-centered">
                <input class="input" type="text" name="toleranceFactor" v-model="toleranceFactor">
            </div>
        </div>  

        <div class="column is-half field has-text-left">
            <label class="label">P-Value*</label>
            <div class="control has-text-centered">
                <input class="input" type="text" name="pvalue" v-model="pvalue">
            </div>
        </div>  
    </div>
        <div class=" columns is-multiline">
            <div class="column is-half has-text-left" >
                <a class="button is-link" @click="send" v-bind:disabled="disabled">Submit</a>
            </div>
             <div class="column is-half has-text-right" v-if="!loading && ready">
                <download-excel
	                class   = "btn btn-default"
                    :data   = "json_data"
	                :fields = "json_fields"
	                name    = "result.xls">
                    Download the result:  <i class="fa fa-file-excel-o" style="font-size:30px;color:green;"></i>
                </download-excel>
            </div>
        </div>
        {{errorMsg}}
    </div>
  </div>

  </form>
</template>

<script>
import axios from 'axios';

export default {
  data: function () {
      return {
        disabled: false,
        loading: false,
        ready : false ,
        file: null,
        fileName: '',
        minProteins: '',
        toleranceFactor: '',
        nullDistributions: '',
        pvalue: '',
        taxonId: '',
        json_data: [ ],
        json_meta: [[{
				"key": "charset",
				"value": "utf-8"
		}]],
        json_fields: {
            'conditionName': 'conditionName',
            'goId': 'goId',
            'geneName': 'geneName',
            'qualifier': 'qualifier',
            'aspect ' : 'aspect',
            'pvalue ' : 'pvalue',
            'qvalue ' : 'qvalue',
            'rank ' : 'rank',
            'weight ' : 'weight',
            'core ' : 'core'
        },
      }
  },
  methods: {
    onFileChanged (event) {
     console.log("entrou onFileChanged");
     this.file = event.target.files[0]
     this.fileName = this.file.name;
    },
    async send() {

        this.loading = true;

        this.ready = false;

        this.json_data = [ ];

        this.disabled = true;

        let error = false;

        let errorMsg = '';

        console.log("file: " + this.file);
        console.log("nullDistributions: " + this.nullDistributions);
        console.log("taxonId: " + this.taxonId);
        console.log("minProteins: " + this.minProteins);
        console.log("pvalue: " + this.pvalue);
        console.log("toleranceFactor: " + this.toleranceFactor);

        if(this.file == '' || this.file == null || this.file == undefined) {
            errorMsg = ' Is necessary to upload a file!' + '\n';
             error = true;
        }

        if(this.taxonId == '' || this.taxonId == null || this.taxonId == undefined){
            errorMsg = errorMsg  + ' Is necessary to select the taxon' + '\n';
            error = true;
        }

        
        if(this.minProteins == '' || this.minProteins == null || this.minProteins == undefined || isNaN(this.minProteins) || this.minProteins <= 0 ) {
            errorMsg = errorMsg + ' Is necessary to set a valid minimum number of proteins! '  + '\n';
            error = true;
        }

        if(this.nullDistributions == '' || this.nullDistributions == null || this.nullDistributions == undefined || isNaN(this.nullDistributions) || this.nullDistributions < 0) {
            errorMsg = errorMsg + ' Is necessary to set a valid number of null distributions! ' + '\n';
            error = true;
        }

        if(this.toleranceFactor == '' || this.toleranceFactor == null || this.toleranceFactor == undefined || isNaN(this.toleranceFactor) || (this.toleranceFactor < 0 && this.toleranceFactor > 1) ){
            errorMsg = errorMsg + ' Is necessary to inform a valid tolerance factor! ' + '\n';
            error = true;
        }

        if(this.pvalue == '' || this.pvalue == null || this.pvalue == undefined || isNaN(this.pvalue) || this.pvalue > 1) {
            errorMsg = errorMsg + ' Is necessary to inform a valid pvalue! ' + '\n';
            error = true;
        }


        if(error) {

            alert(errorMsg);

            this.loading = false;
            this.ready = false;
            this.disabled = false;

        } else {


            let options = { //emulateJSON: true,
                        //emulateHTTP: true,
                        //'timeout': 300000,
                        'Content-Type': 'multipart/form-data'  };

            var formData = new FormData(); 
            formData.append('file', this.file); 
            formData.append('nullDistributions', this.nullDistributions); 
            formData.append('taxonId', this.taxonId); 
            formData.append('minProteins', this.minProteins); 
            formData.append('pvalue', this.pvalue); 
            formData.append('toleranceFactor', this.toleranceFactor); 

            //axios.post('http://127.0.0.1:8081/spring-boot-rest-0.0.1-SNAPSHOT/pes/map', formData, options )
            axios.post('pes-pes.1d35.starter-us-east-1.openshiftapps.com/spring-boot-rest-0.0.1-SNAPSHOT/pes/map', formData, options )
                .then(response => { 

                for(var item in response.data){

                    let jsonAsString = JSON.stringify(response.data[item]);

                    let parsed = JSON.parse(jsonAsString);
                
                    for(var condition in parsed){
                        let conditionAsString = JSON.stringify(parsed[condition]);
                        console.log("conditionAsString" + conditionAsString);
                        let conditionAsObj = JSON.parse(conditionAsString);
                        //console.log("conditionAsObj" + conditionAsObj);
                        this.json_data.push(conditionAsObj);
                    }
                }
                    this.loading = false;
                    this.ready = true;
                    this.disabled = false;
                })
                .catch(e => {
                    this.loading = false;
                    this.disabled = false;
                    this.ready = false;
                    console.log(e);
                })
        }
    }
  }
}
</script>

<style>
.card {
  border-radius: 10px;
}
.card-header-title {
  color: #636368;
}
</style>