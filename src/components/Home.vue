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
    <div>
        <button @click="activeTab = 'detail'">Detail</button>
        <button @click="activeTab = 'timezone'">Timezone</button>
        <button @click="activeTab = 'userAgent'">User Agent</button>
        <button @click="activeTab = 'astronomy'">Astronomy</button>
    </div>

    <div v-if="activeTab === 'detail'">
        <h2>Detail Tab</h2>
        <div v-if="geolocation">
            <p>IP Address: {{ geolocation.ip }}</p>
            <p>Continent: {{  geolocation.continent_name }}</p>
            <p>Country: {{ geolocation.country_name }}</p>
            <p>City: {{ geolocation.city }}</p>
            <!-- Add more fields as needed -->
            <img :src="geolocation.country_flag" alt="Flag" style="max-width: 100px;">
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
        const activeTab = ref('detail');

        // Function to handle search
        const search = async () => {
            loading.value = true;
            try {
                geolocation.value = await fetchGeolocation(ipAddress.value);
                errorMessage.value = '';
                console.log(geolocation.value);
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
            showPopup,
            activeTab
        };
    }
};
</script>