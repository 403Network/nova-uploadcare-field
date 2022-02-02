<template>
    <default-field :field="field">
        <template slot="field">
            <div v-if="uploading">
                <span class="uc-upload-status">Uploading...</span>
            </div>
            <div v-else-if="value" class="mb-3">
                <a class="btn btn-link text-primary cursor-pointer text-80 mr-3" :href="value" target="_blank">View File</a>

                <a class="btn btn-link text-danger opacity-50 cursor-pointer" @click.prevent="clear">Clear</a>
            </div>
            <uploadcare
                    v-show="!uploading"
                    class="btn btn-default btn-primary cursor-pointer"
                    :id="field.name"
                    :publicKey="field.key"
                    imageShrink="2000 x 2000 85%"
                    @progress="progressUpdate"
                    @uploading="uploadingStarted"
                    @completed="uploadingEnded"
                    @success="onSuccess">
                <div v-if="value">Upload new file</div>
                <div v-if="!value">Upload file</div>
            </uploadcare>
            <p v-if="hasError" class="my-2 text-danger">
                {{ firstError }}
            </p>
        </template>
    </default-field>
</template>

<script>
    import {FormField, HandlesValidationErrors} from 'laravel-nova'
    import Uploadcare from 'uploadcare-vue'

    export default {
        mixins: [FormField, HandlesValidationErrors],
        data: () => ({
            uploading: false,
        }),
        props: ['resourceName', 'resourceId', 'field'],

        components: {Uploadcare},

        created() {
        },
        methods: {
            uploadingStarted () {
                this.uploading = true;
                console.log('uploading started')
            },
            uploadingEnded () {
                this.uploading = false;
                console.log('uploading END')
            },
            progressUpdate (e) {
                console.log(e)
            },
            setInitialValue() {
                this.value = this.field.value || ''
            },

            fill(formData) {
                formData.append(this.field.attribute, this.value || '')
            },

            onSuccess(file) {
                console.log(file);
                this.value = file.cdnUrl;
            },

            clear() {
                this.value = null;
            }
        }
    }
</script>

<style scoped>
.uc-upload-status {
    padding: 0 0 1rem;
    display: block;
    font-weight: bold;
}
</style>