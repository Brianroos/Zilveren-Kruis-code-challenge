<template>
    <Datepicker
        v-model="value"
        locale="nl"
        cancelText="Annuleer"
        selectText="Selecteer"
        :enable-time-picker="false"
        :max-date="new Date()"
        placeholder="dd-mm-jjjj"
        :preview-format="format"
        :format="format"
    ></Datepicker>
</template>

<script lang="ts">
import { ref } from 'vue';
import Datepicker from '@vuepic/vue-datepicker';
import '@vuepic/vue-datepicker/dist/main.css';

export default {
    name: 'ZKDatepicker',
    components: { Datepicker },
    data() {
        return {
            value: ''
        };
    },
    setup() {
        const date = ref(new Date());
        const format = (date: Date) => {
            const day = date.getDate();
            const month = date.getMonth() + 1;
            const year = date.getFullYear();

            return `${day}-${month}-${year}`;
        };

        return {
            date,
            format
        };
    }
};
</script>
