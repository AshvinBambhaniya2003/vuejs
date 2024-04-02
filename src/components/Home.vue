<template>
    <!-- <Map /> -->
    <div>
        <input type="text" v-model="ipAddress" placeholder="Enter IP Address">
        <button @click="search">Search</button>
        <div v-if="loading">Loading...</div>
        <div v-if="errorMessage">{{ errorMessage }}</div>
        <div v-if="geolocation">
            <h2>{{ geolocation.city }}, {{ geolocation.country_name }}</h2>
            <p>Latitude: {{ geolocation.latitude }}</p>
            <p>Longitude: {{ geolocation.longitude }}</p>
            <Map :latitude="geolocation.latitude" :longitude="geolocation.longitude" @markerClick="showPopup" />
        </div>
    </div>
</template>

<script>
import { ref } from 'vue';
import Map from "../components/Map.vue";

async function fetchGeolocation(ipAddress) {
    try {
        const response = await fetch(`https://api.ipgeolocation.io/ipgeo?apiKey=868ce398ef03489186c8ed727141cfce&ip=${ipAddress}`);
        if (!response.ok) {
            throw new Error('Network response was not ok');
        }
        return await response.json();
    } catch (error) {
        throw new Error('Error fetching geolocation information');
    }
}

export default {
    components: {
        Map
    },
    setup() {
        const ipAddress = ref('');
        const geolocation = ref(null);
        const loading = ref(false);
        const errorMessage = ref('');

        // Function to handle search
        const search = async () => {
            loading.value = true;
            try {
                geolocation.value = await fetchGeolocation(ipAddress.value);
                errorMessage.value = '';
            } catch (error) {
                geolocation.value = null;
                errorMessage.value = error.message;
            }
            loading.value = false;
        };

        const showPopup = () => {
            // Function to show popup options for saving data in JSON
            // Implement this according to your specific requirements
            console.log("Popup options for saving data in JSON");
        };

        return {
            ipAddress,
            geolocation,
            loading,
            errorMessage,
            search,
            showPopup
        };
    }
};
</script>