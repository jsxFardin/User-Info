<template>
    <!-- Modal -->
    <div class="modal fade" id="globalModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalCenterTitle" aria-hidden="true">
        <div class="modal-dialog modal-dialog-centered" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="modalTitle"></h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                    <slot></slot>

            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: "modal",
        props:["cndition", "modalHeading", "footerButton"]
    }
</script>

<style scoped>

</style>
