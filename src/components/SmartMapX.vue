<template>
  <div id="mapContainer"></div>
</template>

<script setup>
import { computed, onMounted, reactive } from 'vue';
import init from '../utils/initMap';
import * as turf from '@turf/turf';

let map;

let polyInner, polyMiddle, polyOuter, polySuburb;

let polyRegion = reactive({
  inner: polyInner,
  middle: polyMiddle,
  outer: polyOuter,
  suburb: polySuburb,
});

onMounted(() => {
  map = init();

  map.on('load', () => {
    loadShanghaiCityRingArea(map);

    loadShanghaiRealEstatePoint(map);
  })

});

const getRegionBelongTo = (coords) => {
  // calculate which region the input point inside of.
  let res;
  const pt = turf.point(coords);
  for (const region in polyRegion) {
    if (turf.booleanPointInPolygon(pt, polyRegion[region])) {
      res = region;
    }
  }
  if (!res) {
    res = 'unknown';
  }

  return res;
};

const loadShanghaiRealEstatePoint = (map) => {
  fetch(`${import.meta.env.VITE_BUILD_PATH_PREFIX}/geojson/ShanghaiRealEstatePoint.geojson`)
    .then((response) => response.json())
    .then((data) => {
      map.addSource('ShanghaiRealEstatePoint', {
        type: 'geojson',
        data,
      });

      map.addLayer({
        id: 'ShanghaiRealEstatePoint_Layer',
        type: 'circle',
        source: 'ShanghaiRealEstatePoint',
        paint: {
          'circle-radius': 2,
          // 'circle-color': ['get', 'color'],
          // 'circle-stroke-color': ['get', 'color'],
          'circle-stroke-width': 1,
        },
      });

      map.on('click', 'ShanghaiRealEstatePoint_Layer', (e) => {
        // console.log('click ShanghaiRealEstatePoint_Layer e:', e);
        const coordinates = e.features[0].geometry.coordinates.slice();
        const id = e.features[0].properties.id;
        const regionBelongTo = getRegionBelongTo(coordinates);
        const description = `<p>id: ${id}</p><dr/><p>region: ${regionBelongTo}</p>`;

        while (Math.abs(e.lngLat.lng - coordinates[0]) > 180) {
          coordinates[0] += e.lngLat.lng > coordinates[0] ? 360 : -360;
        }

        new smartmapx.Popup({
          closeButton: true,
          closeOnClick: true,
          anchor: 'bottom',
        })
          .setLngLat(coordinates)
          .setHTML(description)
          .addTo(map);
      });

      map.on('mouseenter', 'ShanghaiRealEstatePoint_Layer', () => {
        map.getCanvas().style.cursor = 'pointer';
      });

      map.on('mouseleave', 'ShanghaiRealEstatePoint_Layer', () => {
        map.getCanvas().style.cursor = '';
      });
    
    })
};

const loadShanghaiCityRingArea = (map) => {
  fetch(`${import.meta.env.VITE_BUILD_PATH_PREFIX}/geojson/ShanghaiCityRingArea.geojson`)
    .then((response) => {
      // console.log('response:', response);
      return response.json();
    })
    .then((data) => {
      // console.log('data:', data);
      map.addSource('ShanghaiCityRingArea', {
        type: 'geojson',
        data,
      });

      map.addLayer({
        id: 'ShanghaiCityRingArea_Layer',
        type: 'fill',
        source: 'ShanghaiCityRingArea',
        paint: {
          'fill-color': ['get', 'color'],
          'fill-opacity': 0.5,
        },
      });

      // store the region polygon data.
      data.features.forEach((feature) => {
        const areaName = feature.properties.area_name;
        switch (areaName) {
          case 'inner': {
            polyRegion.inner = turf.multiPolygon(feature.geometry.coordinates);
            break;
          }
          case 'middle': {
            polyRegion.middle = turf.multiPolygon(feature.geometry.coordinates);
            break;
          }
          case 'outer': {
            polyRegion.outer = turf.multiPolygon(feature.geometry.coordinates);
            break;
          }
          case 'suburb': {
            polyRegion.suburb = turf.multiPolygon(feature.geometry.coordinates);
            break;
          }
          default: {
            log.error('Unknown area name:', areaName);
            break;
          }
        }
      });
    });
};




</script>

<style scoped>
#mapContainer {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
}
</style>
