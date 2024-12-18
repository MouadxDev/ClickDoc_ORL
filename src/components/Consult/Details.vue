<script setup lang="ts">
import { Ref, onBeforeMount, reactive, ref } from 'vue';
import { Consultation } from '../../../core/Clients/Consult';
import { useConsultStore } from '../../../core/Data/stores/consultation';

const client = new Consultation();
const consult = useConsultStore();
const consultation : Ref<any> = ref({
    motif : [],
    isPrivate:false,
    isFinished:false,
})

const liste_motifs = reactive([ 
        {label:"Otalgie (douleur à l'oreille) ",value:"Otalgie (douleur à l'oreille) "},
        {label:"Surdité (perte d'audition) ",value:"Surdité (perte d'audition) "},
        {label:"Vertiges",value:"Vertiges"},
        {label:"Acouphènes (bourdonnements ou sifflements dans les oreilles)",value:"Acouphènes (bourdonnements ou sifflements dans les oreilles)"},
        {label:"Rhinorrhée (écoulement nasal)",value:"Rhinorrhée (écoulement nasal)"},
        {label:"Épistaxis (saignement de nez)",value:"Épistaxis (saignement de nez)"},
        {label:"Dysphonie (altération de la voix)",value:"Dysphonie (altération de la voix)"},
        {label:"Dysphagie (difficulté à avaler) ",value:"Dysphagie (difficulté à avaler) "},
        {label:"Adénopathies cervicales (ganglions au niveau du cou) ",value:"Adénopathies cervicales (ganglions au niveau du cou) "},
        {label:"Obstruction nasale ",value:"Obstruction nasale "},
        {label:"Apnée du sommeil ",value:"Apnée du sommeil "},
        {label:"Toux chronique ",value:"Toux chronique "},
        {label:"Autres ",value:"Autres"}
])

const more : Ref<any> = ref("");

async function addMore(){
    consultation.value.motif.push(more.value)
    liste_motifs.push({label:more.value,value:more.value})
    var index = consultation.value.motif.indexOf("Autres");
    if (index !== -1) {
        consultation.value.motif.splice(index, 1);
    }
    await setConsult()
}

async function getConsultation(){
    const data =  await client.getOne(consult.consult);
    return data.data.deets;
}

async function setConsult(){
    await client.update({id:consult.consult,motif:JSON.stringify(consultation.value.motif),isFinished:consultation.value.isFinished,isPrivate:consultation.value.isPrivate})
    consultation.value=await getConsultation()
    consultation.value.motif = JSON.parse(consultation.value.motif)
}

onBeforeMount(async()=>{
    consultation.value=await getConsultation()
    consultation.value.motif = JSON.parse(consultation.value.motif)
    var result : any =[]
    for(var i = 0 ; i<consultation.value.motif.length ; i++)
    {
        result = liste_motifs.filter((word) => word.value == consultation.value.motif[i] );
        if(result.length == 0)
        {
            liste_motifs.push({label:consultation.value.motif[i],value:consultation.value.motif[i]})
        }
        console.log(result)
    }
    
})

</script>
<template>
    <div class="container">
        <el-form label-position="top">
            <el-form-item label="Motifs">
                <el-select-v2
                    v-model="consultation.motif"
                    :options="liste_motifs"
                    placeholder="Selectionner"
					filterable
                    multiple
                    class="w-full"
                    clearable
                    @change="async ()=>{await setConsult()}"
                    :disabled="!consult.edit"
                />
            </el-form-item>
            <el-form-item v-if="consultation.motif.includes('Autres')" >
                <el-input v-model="more" placeholder="Ajoutez le motif" >
                    <template #append>
                        <el-button size="small"  @click="addMore()"> Ajouter</el-button>
                    </template>
                </el-input>
            </el-form-item>
        </el-form>
    </div>
</template>