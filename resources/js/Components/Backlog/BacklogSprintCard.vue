

<script lang="ts" setup>

import { onMounted, reactive} from 'vue'
import "@shoelace-style/shoelace/dist/components/select/select";
import "@shoelace-style/shoelace/dist/components/option/option";
import draggable from "vuedraggable";
import SprintCardMenu from "./SprintCardMenu.vue";
import IssueCard from "./IssueCard.vue";
import axios from "axios";
import IssueCreate from "@/Components/Backlog/IssueCreate.vue";

const props = defineProps({
    sprint: Object,

})


const changeIssueSprintId = (event: any) => {
    //@ts-ignore
    console.log(event)
    if (event.added) {
        console.log(props.sprint?.id)
        console.log(event.added.element.id)
        //@ts-ignore
        axios.post("/api/sprints/" + props.sprint.id + "/issues/" + event.added.element.id).then((response) => {
            fetchSprintIssues()
        })
    }
}

function fetchSprintIssues(){
    //@ts-ignore
    axios.get("/api/sprints/" + props.sprint.id + "/issues").then((response)=>{
        state.issues = response.data
    })



}


onMounted(()=>{
    fetchSprintIssues()

})

const state = reactive({
    issues: null ,
    DeleteDialog : false,
    EditDialog : false,
    startSprint : false,

})




</script>


<template>

    <!--   💕 sprint Card start -->
    <div v-if="$page.props.flash?.success">
        {{ $page.props.flash.success }}
    </div>


<!--    backlog Create Section start  -->
    <section class="default-sprint mb-6  rounded  py-1 bg-gray-200">
        <div class="px-3 my-2">
            <draggable
                v-model="state.issues "
                group="tasks"
                @start="state.drag=true"
                @end="state.drag=false"
                @change="changeIssueSprintId"
                item-key="id"
            >
                <template class="grid  border-dotted border-gray-700 cursor-pointer border-3  align-content-lg-space-between" #item="{element,index}" >

                    <div :key="element.id" class="text-center  w-full  rounded my-3 ">
                        <IssueCard :issue="element" />

                    </div>
                </template>
                <template #header>
                    <div class="flex align-center   justify-space-between">
                        <div class="flex gap-3 ">
                            <i class="fa-duotone fa-angle-down"></i>
                            <p>
                                {{ props.sprint.project_key }}
                                <span>sprint 1</span>
                            </p>
                            <div class="sprint-action flex">
                                <i class="fa-regular fa-pen"></i>
                                <p>
                                    Add Dates ({{ state.issuesLength  }} issuses)
                                </p>
                            </div>
                        </div>
                        <div class="flex gap-1">
                            <button class="bg-gray-100 mr-2 hover:bg-gray-50 px-2 py-1 rounded "
                            @click="state.startSprint = !state.startSprint"
                            >
                                {{ !state.startSprint ? 'Start sprint' : 'Stop sprint' }}
                            </button>
                            <SprintCardMenu :project="props.sprint" />
                        </div>

                    </div>
                </template>
                <template #footer class="my-auto">
                    <issue-create :id_sprint="props.sprint.id" @getIssues="fetchSprintIssues" />
                </template>
            </draggable>
        </div>
    </section>
    <!--  😍 sprint Card end -->

</template>


<style scoped>


</style>
