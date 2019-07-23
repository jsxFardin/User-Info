<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card card-default">
                    <div class="card-header">Profile Component</div>

                    <div class="card-body">
                        I'm an profile component.
                    </div>
                </div>
            </div>
        </div>


        <modal modalHeading="Profile Heading">
            <form>
            <div class="modal-body">
                Test
            </div>

                <div class="modal-footer">
                    <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
                    <button type="submit" class="btn btn-primary">Profile</button>
                </div>
            </form>
        </modal>
    </div>
</template>

<script>
    export default {
        name: "profile"
    }
</script>

