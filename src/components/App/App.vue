<template>
    <div id="app" class="main-placeholder">
        <ZKHeader />
        <!-- CONTENT HERE -->
        <div class="container my-3">
            <div class="row">
                <div class="col-8">
                    <ZKForm />
                </div>
                <div class="col-4">
                    <ZKContactBlock />
                </div>
            </div>
        </div>
        <ZKFooter />
    </div>
</template>

<script lang="ts">
import ZKHeader from '../Header/ZKHeader.vue';
import ZKForm from '../Form/ZKForm.vue';
import ZKContactBlock from '../ContactBlock/ZKContactBlock.vue';
import ZKFooter from '../Footer/ZKFooter.vue';

export default {
    name: 'App',
    components: {
        ZKHeader,
        ZKForm,
        ZKContactBlock,
        ZKFooter
    }
};
</script>

<style lang="scss">
@import '@/assets/scss/global';
</style>
