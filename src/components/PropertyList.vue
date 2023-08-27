<template>
    <div>
        <h1>Property List</h1>
        <div style="display: flex; padding:2rem 0">
            <el-form :model="filters" @submit.prevent="getPropertyList">
                <el-form-item label="Name">
                    <el-input v-model="filters.name" placeholder="Name"></el-input>
                </el-form-item>
                <el-form-item label="Bedrooms">
                    <el-input v-model="filters.bedrooms" placeholder="Bedrooms"></el-input>
                </el-form-item>
                <el-form-item label="Bathrooms">
                    <el-input v-model="filters.bathrooms" placeholder="Bathrooms"></el-input>
                </el-form-item>
                <el-form-item label="Storeys">
                    <el-input v-model="filters.storeys" placeholder="Storeys"></el-input>
                </el-form-item>
                <el-form-item label="Garages">
                    <el-input v-model="filters.garages" placeholder="Garages"></el-input>
                </el-form-item>
                <el-form-item label="Price">
                    <el-slider v-model="filters.price_range" range :max="max_price" :step="100" :min="10000" />
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="getPropertyList">Search</el-button>
                </el-form-item>
            </el-form>
        </div>
        <el-table v-loading="loading" :data="property_list" stripe style="width: 100%">

            <el-table-column prop="name" label="Name" width="180" />
            <el-table-column prop="bedrooms" label="Bedrooms" width="180" />
            <el-table-column prop="bathrooms" label="Bathrooms" wdth="180" />
            <el-table-column prop="storeys" label="Storeys" width="180" />
            <el-table-column prop="garages" label="Garages" width="180" />
            <el-table-column prop="price" label="Price" width="180" />

        </el-table>
    </div>
</template>

<script setup>

import { ref, onMounted } from 'vue'
import axios from 'axios'
import config from '../config';

const property_list = ref([])

const max_price = ref(1000000);
const price_range = ref([10000, 1000000]);
const filters = ref({
    name: '',
    bedrooms: '',
    bathrooms: '',
    storeys: '',
    garages: '',
    price_range: price_range.value
})

// watch filters and update url search string accordingly

const loading = ref(false);
const axios_config = {
    headers: {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*',
    },
    timeout: 10000,
}

const getPropertyList = async () => {
    loading.value = true;
    const query_string = Object.keys(filters.value).map(key => `${key}=${filters.value[key]}`).join('&');

    history.pushState({}, '', `?${query_string}`)
    let response = null;

    try {
        response = await axios.get(`${config.API_BASE_URL}/properties?${query_string}`, axios_config)
        property_list.value = response.data.data

    } catch (error) {
        console.log('error', error)
    }

    setTimeout(() => {

        loading.value = false;
        if (response && response.data.data.length === 0) {
            ElMessage({
                message: 'No results found',
                type: 'info',
                duration: 2000,
                offset: 100,
                customClass: 'message-box',
                center: true,
                showClose: true,
                iconClass: 'el-icon-info',
                dangerouslyUseHTMLString: true,
                onClose: () => console.log('closed'),
            })
        }
    }, 1000);

}

onMounted(async () => {
    await getPropertyList()
})

</script>