<script lang="ts">
  import MapLibre from '$lib/MapLibre.svelte';
  import GeoJSON from '$lib/GeoJSON.svelte';
  import FillLayer from '$lib/FillLayer.svelte';
  import LineLayer from '$lib/LineLayer.svelte';
  import { mapClasses } from '../styles.js';
  import code from './+page.svelte?raw';
  import CodeSample from '$site/CodeSample.svelte';
  import states from '$site/states.json?url';
  import type { FeatureCollection } from 'geojson';

  let showBorder = true;
  let showFill = true;
  let fillColor = '#006600';
  let borderColor = '#003300';
  let selected: 'light' | 'dark' = 'light';
  $: style =
    selected === 'light'
      ? 'https://basemaps.cartocdn.com/gl/positron-gl-style/style.json'
      : 'https://basemaps.cartocdn.com/gl/dark-matter-gl-style/style.json';

  const coloradoPolygon = {
    type: 'FeatureCollection',
    features: [
      {
        type: 'Feature',
        properties: {},
        geometry: {
          type: 'Polygon',
          coordinates: [
            [
              [-109, 37],
              [-102, 37],
              [-102, 41],
              [-109, 41],
              [-109, 37],
            ],
          ],
        },
      },
    ],
  } as FeatureCollection;

  let dataOption: 'states' | 'colorado' = 'states';
  $: dataset = dataOption === 'states' ? states : coloradoPolygon;
</script>

<div class="controls">
  <select class="controls-select" bind:value={selected}>
    <option value="light">Light</option>
    <option value="dark">dark</option>
  </select>

  <select class="controls-select" bind:value={dataOption}>
    <option value="states">States Dataset</option>
    <option value="colorado">Colorado Dataset</option>
  </select>
</div>

<MapLibre {style} class={mapClasses} standardControls center={[-98.137, 40.137]} zoom={4}>
  <GeoJSON id="states" data={dataset} promoteId="STATEFP">
    {#if showFill}
      <FillLayer
        paint={{
          'fill-color': fillColor,
          'fill-opacity': 0.5,
        }}
        beforeLayerType="symbol"
      />
    {/if}
    {#if showBorder}
      <LineLayer
        layout={{ 'line-cap': 'round', 'line-join': 'round' }}
        paint={{ 'line-color': borderColor, 'line-width': 3 }}
        beforeLayerType="symbol"
      />
    {/if}
  </GeoJSON>
</MapLibre>

<CodeSample {code} />

<style>
  .controls {
    display: flex;
    flex-direction: row;
    gap: 1rem;
    padding: 0.5rem;
  }

  .controls-select {
    box-sizing: border-box;
    padding: 10px 20px;
  }
</style>
