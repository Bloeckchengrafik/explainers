<script lang="ts">
    import Preloader from "$lib/Preloader.svelte";
    import AtomModels from "./AtomModels.svelte";
    import {onMount} from "svelte";
    import chalkBoyUrl from "./chalkboy.glb?url";
    import {GLTFLoader} from "three/examples/jsm/loaders/GLTFLoader.js";
    import type {GLTF} from "three/examples/jsm/loaders/GLTFLoader.js";

    let loaded = false;
    let chalkBoy: GLTF;

    onMount(async () => {
        const loader = new GLTFLoader();
        chalkBoy = await loader.loadAsync(chalkBoyUrl);
        await new Promise(resolve => setTimeout(resolve, 1000));
        loaded = true;
    });
</script>

{#if loaded}
    <AtomModels {chalkBoy} />
{:else}
    <Preloader/>
{/if}
