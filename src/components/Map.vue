<template>
    <div ref="mapContainer" style="height: 400px; width: 400px"></div>
</template>

<script>
import { onMounted, ref, watch } from 'vue';
import L from 'leaflet'

export default {
    props: {
        latitude: {
            type: String,
            required: true
        },
        longitude: {
            type: String,
            required: true
        }
    },
    setup(props) {
        const map = ref();
        const mapContainer = ref();
        const marker = ref()

        onMounted(() => {
            map.value = L.map(mapContainer.value).setView([props.latitude, props.longitude], 13);
            L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
                maxZoom: 19,
                attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
            }).addTo(map.value);

            marker.value = L.marker([props.latitude, props.longitude]).addTo(map.value);

        });

        watch([() => props.latitude, () => props.longitude], ([newLat, newLon]) => {
            if (map.value) {
                map.value.setView([newLat, newLon]);
                if (props.showMarker) {
                    if (marker.value) {
                        marker.value.setLatLng([newLat, newLon]);
                    } else {
                        marker.value = L.marker([newLat, newLon]).addTo(map.value);
                    }
                } else if (marker.value) {
                    map.value.removeLayer(marker.value);
                    marker.value = null;
                }
            }
        });

        return {
            map,
            mapContainer,
            marker
        };
    }
};
</script>

<style></style>
