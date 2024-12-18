<script setup lang="ts">
    import { Ref , onBeforeMount, ref } from "vue";
    import {useConsultStore} from "../../../core/Data/stores/consultation"
    import { ExamenPhysique } from '../../../core/Clients/Examen';

    const consult = useConsultStore();
    const examenClient = new ExamenPhysique()

    const data_visage = [
    {label:"Douleur à l'oreille (otalgie)",value:"Douleur à l'oreille (otalgie)"},
    {label:"Perte auditive",value:"Perte auditive"},
    {label:"Écoulement de l'oreille (otorrhée)",value:"Écoulement de l'oreille (otorrhée)"},
    {label:"Sensation de plénitude ou de pression dans l'oreille",value:"Sensation de plénitude ou de pression dans l'oreille"},
    {label:"Bourdonnements dans l'oreille (acouphènes)",value:"Bourdonnements dans l'oreille (acouphènes)"},
    {label:"Vertiges ou sensations de vertiges (vertiges)",value:"Vertiges ou sensations de vertiges (vertiges)"},
    {label:"Autres",value:"Autres"}
]
const data_corps  = [
    {label:"Congestion nasale",value:"Congestion nasale"},
    {label:"Écoulement nasal (rhinorrhée)",value:"Écoulement nasal (rhinorrhée)"},
    {label:"Éternuements",value:"Éternuements"},
    {label:"Obstruction nasale",value:"Obstruction nasale"},
    {label:"Perte de l'odorat (anosmie)",value:"Perte de l'odorat (anosmie)"},
    {label:"Sensation de pression faciale ou de douleur (sinusite)",value:"Sensation de pression faciale ou de douleur (sinusite)"},
    {label:"Autres",value:"Autres"}
]
const data_ongles = [
    {label:"Mal de gorge (pharyngite)",value:"Mal de gorge (pharyngite)"},
    {label:"Enrouement de la voix (dysphonie)",value:"Enrouement de la voix (dysphonie)"},
    {label:"Difficulté à avaler (dysphagie)",value:"Difficulté à avaler (dysphagie)"},
    {label:"Sensation de gorge irritée ou sèche",value:"Sensation de gorge irritée ou sèche"},
    {label:"Toux persistante ou chronique",value:"Toux persistante ou chronique"},
    {label:"Amygdales enflées ou douloureuses",value:"Amygdales enflées ou douloureuses"}
]
const data_cheveux= [
    {label:"Gonflement du cou :",value:"Gonflement du cou :"},
    {label:"Présence de bosses ou de masses dans le cou",value:"Présence de bosses ou de masses dans le cou"},
    {label:"Sensation de gonflement ou d'enflure au niveau des ganglions lymphatiques du cou",value:"Sensation de gonflement ou d'enflure au niveau des ganglions lymphatiques du cou"},
    {label:"Douleur cervicale :",value:"Douleur cervicale :"},
    {label:"Douleur localisée dans la région du cou",value:"Douleur localisée dans la région du cou"},
    {label:"Sensation de raideur ou de tension musculaire dans le cou",value:"Sensation de raideur ou de tension musculaire dans le cou"},
    {label:"Difficulté à avaler :",value:"Difficulté à avaler :"},
    {label:"Sensation de gêne ou de blocage lors de la déglutition",value:"Sensation de gêne ou de blocage lors de la déglutition"},
    {label:"Douleur en avalant (odynophagie)",value:"Douleur en avalant (odynophagie)"}
]
    const examen : Ref<any> = ref({})

    async function getExamenPhysique(){
        const data :any =  await examenClient.getByID(consult.examen_id)
        return {
            id:data.id,
            hair : JSON.parse(data.hair),
            nails : JSON.parse(data.nails),
            face : JSON.parse(data.face),
            body : JSON.parse(data.body)
        }
    }
    async function setExamenPhysique(){
        await examenClient.update(examen.value)
        examen.value = await getExamenPhysique()
    }

    onBeforeMount(async ()=>{
        examen.value = await getExamenPhysique()
    })
</script>

<template>
    <el-form label-position="top">
        <el-form-item label="OREILLE">
            <el-select-v2
                v-model="examen.face"
                :options="data_visage"
                placeholder="Selectionner"
                multiple
				filterable
                class="w-full"
                clearable
                @change="async ()=>{await setExamenPhysique()}"
                :disabled="!consult.edit"
            />
        </el-form-item>
        <el-form-item label="NEZ ET SINUS">
            <el-select-v2
                v-model="examen.body"
                :options="data_corps"
                placeholder="Selectionner"
                multiple
				filterable
                class="w-full"
                clearable
                @change="async()=>{await setExamenPhysique()} "
                :disabled="!consult.edit"
            />
        </el-form-item>
        <el-form-item label="GORGE ET PHARYNX">
            <el-select-v2
                v-model="examen.nails"
                :options="data_ongles"
                placeholder="Selectionner"
				filterable
                multiple
                class="w-full"
                clearable
                @change="async ()=>{await setExamenPhysique()}"
                :disabled="!consult.edit"
            />
        </el-form-item>
        <el-form-item label="COU">
            <el-select-v2
                v-model="examen.hair"
                :options="data_cheveux"
                placeholder="Selectionner"
                multiple
                filterable
				class="w-full"
                clearable
                @change="async ()=>{await setExamenPhysique()}"
                :disabled="!consult.edit"
            />
        </el-form-item>
    </el-form>
</template>