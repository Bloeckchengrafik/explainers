<script lang="ts">
    import * as SC from 'svelte-cubed';
    import type {GLTF} from "three/examples/jsm/loaders/GLTFLoader.js";
    import * as THREE from 'three';
    import {fade} from "svelte/transition";

    export let chalkBoy: GLTF;

    let scroll = 0;

    function lerp(start: number, end: number, amt: number) {
        return (1 - amt) * start + amt * end
    }

    function isActiveDemokrit(scroll: number): boolean {
        return scroll > 0.1
    }

    function isActiveDalton(scroll: number): boolean {
        return scroll > 1.2
    }

    function isActiveThomson(scroll: number): boolean {
        return scroll > 3.3
    }

    function isActiveRutherford(scroll: number): boolean {
        return scroll > 5.0
    }

    function isActiveBohr(scroll: number): boolean {
        return scroll > 6.8
    }

    function isActiveSchroedinger(scroll: number): boolean {
        return scroll > 9
    }

    function chalkBoyPosition(scroll: number): [number, number, number] {
        let position: [number, number, number] = [-1, -0.5, 0]

        if (scroll < 0.5) {
            position[1] = lerp(-3, -0.5, scroll / 0.5)
        } else if (scroll > 1) {
            position[1] = lerp(-0.5, -3, (scroll - 1) / 0.5)
        }

        return position
    }

    function chalkBoyRotation(scroll: number): [number, number, number] {
        let rotation: [number, number, number] = [0, 0, 0]

        // after first quarter, rotate the face to the user
        if (scroll > 0.25 && scroll < 0.75) {
            let segment = (scroll - 0.25) / 0.5
            let from = 0
            let to = Math.PI / 2 - 1

            rotation[1] = lerp(from, to, segment)
        } else if (scroll > 0.75) {
            rotation[1] = Math.PI / 2 - 1
        }

        return rotation
    }

    function demokritTextTop(scroll: number): number {
        let top = 0

        // on first half, do nothing
        // after, move the text up 1/4 viewport height
        if (scroll > 1) {
            top = lerp(0, -25, (scroll - 1) / 0.5)
        }

        return top
    }

    function demokritTextOpacity(scroll: number): number {
        let opacity = 1

        // first 10th, fade in
        if (scroll < 0.1) {
            opacity = lerp(0, 1, scroll / 0.1)
        }

        // on first half, do nothing
        // after, move the text up 1/4 viewport height
        if (scroll > 1) {
            opacity = lerp(1, 0, (scroll - 1) / 0.5)
        }

        return opacity
    }


    function daltonTextTop(scroll: number): number {
        let top = 0

        // start a bit at the bottom, then move up at 1.5, stay there until 2.7, then move up again
        if (scroll < 1.5) {
            top = lerp(25, 0, scroll / 1.5)
        } else if (scroll > 2.7) {
            top = lerp(0, -25, (scroll - 2.7) / 0.5)
        }

        return top
    }

    function daltonTextOpacity(scroll: number): number {
        let opacity = 1

        // first 10th, fade in
        if (scroll < 1.5) {
            opacity = 0
        }

        if (scroll < 2.2) {
            opacity = lerp(0, 1, (scroll - 1.5) / 0.7)
        }

        // on first half, do nothing
        // after, move the text up 1/4 viewport height
        if (scroll > 2.7) {
            opacity = lerp(1, 0, (scroll - 2.7) / 0.5)
        }

        return opacity
    }

    function thomsonTextTop(scroll: number): number {
        let top = 0

        // start a bit at the bottom, then move up at 1.5, stay there until 2.7, then move up again
        if (scroll < 3.5) {
            top = lerp(25, 0, scroll / 3.5)
        } else if (scroll > 4.7) {
            top = lerp(0, -25, (scroll - 4.7) / 0.5)
        }

        return top
    }

    function thomsonTextOpacity(scroll: number): number {
        let opacity = 1

        // first 10th, fade in
        if (scroll < 3.5) {
            opacity = 0
        }

        if (scroll < 4.2) {
            opacity = lerp(0, 1, (scroll - 3.5) / 0.7)
        }

        // on first half, do nothing
        // after, move the text up 1/4 viewport height
        if (scroll > 4.7) {
            opacity = lerp(1, 0, (scroll - 4.7) / 0.5)
        }

        return opacity
    }

    function randomPosInCircle(radius: number): number[] {
        let angle = Math.random() * Math.PI * 2
        let x = Math.cos(angle) * radius
        let y = Math.sin(angle) * radius

        // return [x, y] - for the edge of the circle - we want somewhere in the middle
        return [x / (Math.random() + 3), y / (Math.random() + 3)].map(x => x + 50)
    }

    function rutherfordTextTop(scroll: number): number {
        let top = 0

        // start a bit at the bottom, then move up at 1.5, stay there until 2.7, then move up again
        if (scroll < 5.5) {
            top = lerp(25, 0, scroll / 5.5)
        } else if (scroll > 6.7) {
            top = lerp(0, -25, (scroll - 6.7) / 0.5)
        }

        return top
    }

    function rutherfordTextOpacity(scroll: number): number {
        let opacity = 1

        // first 10th, fade in
        if (scroll < 5.5) {
            opacity = 0
        }

        if (scroll < 6.2) {
            opacity = lerp(0, 1, (scroll - 5.5) / 0.7)
        }

        // on first half, do nothing
        // after, move the text up 1/4 viewport height
        if (scroll > 6.7) {
            opacity = lerp(1, 0, (scroll - 6.7) / 0.5)
        }

        return opacity
    }

    function notRandom(seed: number) {
        const x = Math.sin(seed++) * 10000;
        return x - Math.floor(x);
    }

    function fixedRandomCirclePosition(maxRadius: number, minRadius: number, scroll: number, seed: number) {
        seed *= 34580923;
        // random position in circle + rotation around center
        let angle = notRandom(seed) * Math.PI * 2
        let radius = notRandom(seed * 2) * (maxRadius - minRadius) + minRadius
        let x = Math.cos(angle) * radius
        let y = Math.sin(angle) * radius

        // rotate around center
        let rotation = scroll * Math.PI * 2
        let rotatedX = x * Math.cos(rotation) - y * Math.sin(rotation)
        let rotatedY = x * Math.sin(rotation) + y * Math.cos(rotation)

        return [rotatedX, rotatedY].map(x => x + 50)
    }

    function deg2rad(degrees: number) {
        return degrees * Math.PI / 180;
    }

    function circlePositionOnDegrees(degrees: number, radius: number, scroll: number) {
        let angle = deg2rad(degrees + scroll * 360)
        let x = Math.cos(angle) * radius
        let y = Math.sin(angle) * radius

        return [x, y].map(x => x + 50)
    }

    function bohrTextTop(scroll: number): number {
        let top = 0

        // start a bit at the bottom, then move up at 1.5, stay there until 2.7, then move up again
        if (scroll < 7.5) {
            top = lerp(25, 0, scroll / 7.5)
        } else if (scroll > 8.7) {
            top = lerp(0, -25, (scroll - 8.7) / 0.5)
        }

        return top
    }

    function bohrTextOpacity(scroll: number): number {
        let opacity = 1

        // first 10th, fade in
        if (scroll < 7.5) {
            opacity = 0
        }

        if (scroll < 8.2) {
            opacity = lerp(0, 1, (scroll - 7.5) / 0.7)
        }

        // on first half, do nothing
        // after, move the text up 1/4 viewport height
        if (scroll > 8.7) {
            opacity = lerp(1, 0, (scroll - 8.7) / 0.5)
        }

        return opacity
    }

    function scatterAroundCenter(radius: number, seed: number, spread: number) {
        let angle = notRandom(seed) * Math.PI * 2
        // try to focus around the center
        let rndmRadius = notRandom(seed * 2) * radius * spread
        rndmRadius = Math.pow(rndmRadius, 2) / radius
        let x = Math.cos(angle) * rndmRadius
        let y = Math.sin(angle) * rndmRadius

        // rotate around center
        return [x, y].map(x => x + 50)
    }

    function schroedingerTextTop(scroll: number): number {
        let top = 0

        // start a bit at the bottom, then move up at 1.5, stay there until 2.7, then move up again
        if (scroll < 9.5) {
            top = lerp(25, 0, scroll / 9.5)
        } else if (scroll > 10.7) {
            top = lerp(0, -25, (scroll - 10.7) / 0.5)
        }

        return top
    }

    function schroedingerTextOpacity(scroll: number): number {
        let opacity = 1

        // first 10th, fade in
        if (scroll < 9.5) {
            opacity = 0
        }

        if (scroll < 10.2) {
            opacity = lerp(0, 1, (scroll - 9.5) / 0.7)
        }

        // on first half, do nothing
        // after, move the text up 1/4 viewport height
        if (scroll > 10.7) {
            opacity = lerp(1, 0, (scroll - 10.7) / 0.5)
        }

        return opacity
    }
</script>
<svelte:window on:scroll={() => scroll = window.scrollY / window.innerHeight}/>

<div class="fixed top-0 left-0 w-full h-full">
    <SC.Canvas
            antialias
            background={new THREE.Color('#1d232a')}
            fog={new THREE.FogExp2('#1d232a', 0.1)}
            shadows
    >
        <SC.Primitive
                object={chalkBoy.scene}
                scale={[0.1, 0.1, 0.1]}
                rotation={chalkBoyRotation(scroll)}
                position={chalkBoyPosition(scroll)}
        />
        <SC.AmbientLight intensity={0.6}/>
        <SC.DirectionalLight intensity={0.6} position={[-2, 3, 2]} shadow={{ mapSize: [2048, 2048] }}/>
        <SC.PerspectiveCamera position={[1, 1, 3]}/>
        <SC.OrbitControls
                enablePan={false}
                enableDamping
                dampingFactor={0.1}
                enableZoom={false}
        />
    </SC.Canvas>
</div>
<div class="fixed top-0 left-0 w-full h-full flex justify-center items-center">
    <div class="flex-grow flex justify-center items-center">
        <div class="transition-all opacity-0 m-4" class:opacity-100={scroll > 1.4 && scroll < 3}>
            <div class="rounded-full w-20 h-20 bg-red-500 flex justify-center items-center text-4xl font-bold text-white">
                H
            </div>
        </div>

        <div class="transition-all opacity-0 m-4" class:opacity-100={scroll > 1.5 && scroll < 3.1}>
            <div class="rounded-full w-32 h-32 bg-blue-500 flex justify-center items-center text-5xl font-bold text-white">
                Na
            </div>
        </div>

        <div class="transition-all opacity-0 m-4" class:opacity-100={scroll > 1.6 && scroll < 3.2}>
            <div class="rounded-full w-40 h-40 bg-green-500 flex justify-center items-center text-6xl font-bold text-white">
                Kr
            </div>
        </div>
    </div>
    <div class="flex-grow"></div>
</div>

<div class="fixed top-0 left-0 w-full h-full flex justify-center items-center">
    <div class="flex-grow flex justify-center items-center">
        <div class="transition-all opacity-0 m-4 ml-8" class:opacity-100={scroll > 3.4 && scroll < 4.8}>
            <div class="rounded-full w-96 h-96 bg-red-500 flex justify-center items-center text-4xl font-bold text-white relative">
                <span class="text-[7em] font-bold text-slate-400">+</span>
                {#each Array(10) as _, i}
                    {#if scroll > 3.8 + i * 0.03 && scroll < 4.8 + i * 0.03}
                        <div class="absolute w-10 h-10 bg-white tup rounded-full flex justify-center items-center text-blue-700"
                             style="top: {randomPosInCircle(100)[0]}%; left: {randomPosInCircle(100)[1]}%; transform: translate(-50%, -50%);"
                             transition:fade>
                            -
                        </div>
                    {/if}
                {/each}
            </div>
        </div>
    </div>
    <div class="flex-grow"></div>
</div>

<div class="fixed top-0 left-0 w-full h-full flex justify-center items-center">
    <div class="flex-grow flex justify-center items-center">
        <div class="transition-all opacity-0 m-4 ml-8" class:opacity-100={scroll > 5.4 && scroll < 8.8}>
            <div class="rounded-full w-96 h-96 border-2 border-blue-500 flex justify-center items-center text-4xl font-bold text-white relative">
                {#if scroll < 6.8}
                    {#each Array(10) as _, i}
                        <div class="absolute w-10 h-10 bg-white tup rounded-full flex justify-center items-center text-blue-700"
                             style="top: {fixedRandomCirclePosition(20, 45, scroll, i)[0]}%; left: {fixedRandomCirclePosition(20, 45, scroll, i)[1]}%; transform: translate(-50%, -50%);"
                             transition:fade>
                            -
                        </div>
                    {/each}
                    <div class="rounded-full w-16 h-16 bg-red-500 flex justify-center items-center text-4xl font-bold text-white relative"></div>
                {:else}
                    {#each Array(2) as _, i}
                        <div class="absolute w-8 h-8 bg-white tup rounded-full flex justify-center items-center text-blue-700"
                             style="top: {circlePositionOnDegrees(180*i, 15, scroll)[0]}%; left: {circlePositionOnDegrees(180*i, 15, scroll)[1]}%; transform: translate(-50%, -50%);"
                             transition:fade>
                            -
                        </div>
                    {/each}
                    <div class="rounded-full w-[18rem] h-[18rem] border-2 border-blue-500 flex justify-center items-center text-4xl font-bold text-white relative">
                        {#each Array(8) as _, i}
                            <div class="absolute w-8 h-8 bg-white tup rounded-full flex justify-center items-center text-blue-700"
                                 style="top: {circlePositionOnDegrees((360/8)*i, 43, scroll/2)[0]}%; left: {circlePositionOnDegrees((360/8)*i, 43, scroll/2)[1]}%; transform: translate(-50%, -50%);"
                                 transition:fade>
                                -
                            </div>
                        {/each}
                        <div class="rounded-full w-[12rem] h-[12rem] border-2 border-blue-500 flex justify-center items-center text-4xl font-bold text-white relative">
                            {#each Array(4) as _, i}
                                <div class="absolute w-8 h-8 bg-white tup rounded-full flex justify-center items-center text-blue-700"
                                     style="top: {circlePositionOnDegrees((360/4)*i, 90, scroll/4)[0]}%; left: {circlePositionOnDegrees((360/4)*i, 90, scroll/4)[1]}%; transform: translate(-50%, -50%);"
                                     transition:fade>
                                    -
                                </div>
                            {/each}
                            <div class="rounded-full w-16 h-16 bg-red-500 flex justify-center items-center text-4xl font-bold text-white relative"></div>
                        </div>
                    </div>
                {/if}
            </div>
        </div>
    </div>
    <div class="flex-grow"></div>
</div>

<div class="fixed top-0 left-0 w-full h-full flex justify-center items-center">
    <div class="flex-grow flex justify-center items-center">
        <div class="transition-all opacity-0 m-4 ml-8" class:opacity-100={scroll > 9}>
            <div class="w-96 h-96 flex justify-center items-center text-4xl font-bold text-white relative after-name">
                {#each Array(4000) as _, i}
                    {#if scroll > 9.5 + i * 0.0001}
                        <div class="absolute w-1 h-1 bg-blue-400 tup rounded-full flex justify-center items-center text-blue-700"
                             style="top: {scatterAroundCenter(45, i, 1)[0]}%; left: {scatterAroundCenter(45, i, 1)[1]}%; transform: translate(-50%, -50%);"></div>
                    {/if}
                {/each}

                {#each Array(400) as _, i}
                    <div class="absolute w-1 h-1 bg-red-400 tup rounded-full flex justify-center items-center text-blue-700"
                         style="top: {scatterAroundCenter(45, i, 0.3)[0]}%; left: {scatterAroundCenter(45, i, 0.3)[1]}%; transform: translate(-50%, -50%);"></div>
                {/each}

                <div class="absolute w-10 h-10 shadow-2xl theshadow bg-neutral-200 tag flex justify-center items-center text-blue-700 z-[99999999]
                        bottom-[50%] left-[50%]"
                >
                </div>
                <div class="absolute w-10 h-10 theshadow flex justify-center items-center text-white z-[99999999]
                        bottom-[calc(50%+25px)] left-[calc(50%+40px)]"
                >
                    Kern
                </div>

                <div class="absolute w-10 h-10 theshadow bg-neutral-200 tag flex justify-center items-center text-blue-700 z-[99999999]
                        bottom-[70%] left-[30%]"
                >
                </div>
                <div class="absolute h-10 shadow-2xl theshadow flex justify-center items-center text-white z-[99999999]
                        bottom-[calc(70%+25px)] left-[calc(30%+20px)]"
                >
                    Elektronenchance
                </div>
            </div>
        </div>
    </div>
    <div class="flex-grow"></div>
</div>

<div class="h-[1150vh] z-[9999]">
    <div class="hero min-h-screen fixed justify-end"
         style="top: {demokritTextTop(scroll)}vh; opacity: {demokritTextOpacity(scroll)}">
        <div class="hero-content flex-col text-right">
            <div>
                <h1 class="text-5xl font-bold">Atommodell nach Demokrit</h1>
                <p class="text-2xl">
                    <span class="text-red-500">Demokrit</span> war ein griechischer Philosoph, der im 5. Jahrhundert v.
                    Chr. lebte.
                    <br/>Er war der erste, der die Existenz von Atomen postulierte. <br/>Er nannte sie <span
                        class="text-red-500">Atomos</span>,
                    was so viel wie <span class="text-red-500">unteilbar</span> bedeutet.
                </p>
                <p class="text-2xl mt-20 opacity-0 transition-opacity" class:opacity-100={scroll > 0.4}>
                    Es war noch nichts über die Struktur der Atome bekannt. <br/>
                    Demokrit stellte sich die Atome als winzige, unteilbare Elemente vor, <br/>
                    die sich in einem leeren Raum bewegen.
                </p>
                <p class="text-2xl mt-20 opacity-0 transition-opacity" class:opacity-100={scroll > 0.8}>
                    Demokrit fand dies heraus, indem er ein Stück Kreide immer weiter zerkleinerte. <br/>
                    Er stellte fest, dass es einen Punkt gab, an dem es nicht mehr möglich war, <br/>
                    ob dieses dem heutigen Atom entspricht, ist jedoch fraglich.
                </p>
            </div>
        </div>
    </div>

    <div class="hero min-h-screen fixed justify-end"
         style="top: {daltonTextTop(scroll)}vh; opacity: {daltonTextOpacity(scroll)}">
        <div class="hero-content flex-col text-right">
            <div>
                <h1 class="text-5xl font-bold">Atommodell nach Dalton</h1>
                <p class="text-2xl">
                    <span class="text-red-500">John Dalton</span> war ein englischer Naturforscher, der im 19.
                    Jahrhundert
                    lebte. <br/>
                    Für ihn war das Atom ein winziges, immer noch <span class="text-red-500">untrennbares</span>
                    Teilchen. <br/>
                    Er stellte sich das Atom als winzige Kugel vor, <br/> die von Element zu Element <span
                        class="text-red-500">unterschiedlich</span> ist.
                </p>
                <p class="text-2xl mt-20 opacity-0 transition-opacity" class:opacity-100={scroll > 2}>
                    Atome können sich hier in <span class="text-red-500">Volumen und Masse</span> unterscheiden. <br/>
                    In chemischen Reaktionen werden die Atome nicht zerstört oder geschaffen, <br/>
                    sondern nur <span class="text-red-500">neu angeordnet</span>.
                </p>
            </div>
        </div>
    </div>

    <div class="hero min-h-screen fixed justify-end"
         style="top: {thomsonTextTop(scroll)}vh; opacity: {thomsonTextOpacity(scroll)}">
        <div class="hero-content flex-col text-right">
            <div>
                <h1 class="text-5xl font-bold">Atommodell nach Thomson</h1>
                <p class="text-2xl">
                    <span class="text-red-500">J. J. Thomson</span> war ein englischer Physiker, der im 19.
                    Jahrhundert lebte. <br/>
                    In einem Experiment konnte er nachweisen, <br/> dass Atome aus <span class="text-red-500">kleineren
                        Teilchen</span> bestehen. <br/>
                    Er stellte sich das Atom als <span class="text-red-500">positiv geladenen</span> Ball vor, <br/>
                    in dem <span class="text-red-500">negativ geladene Elektronen</span> verteilt sind.
                </p>
                <p class="text-2xl mt-20 opacity-0 transition-opacity" class:opacity-100={scroll > 4}>
                    Die Elektronen sind in der positiven Ladung <span class="text-red-500">eingebettet</span>. <br/>
                    Das Atom ist also <span class="text-red-500">elektrisch neutral</span>. <br/>
                    Bei der Abgabe oder Aufnahme von Elektronen entsteht ein <span class="text-red-500">Ion</span>.
                </p>
            </div>
        </div>
    </div>

    <div class="hero min-h-screen fixed justify-end"
         style="top: {rutherfordTextTop(scroll)}vh; opacity: {rutherfordTextOpacity(scroll)}">
        <div class="hero-content flex-col text-right">
            <div>
                <h1 class="text-5xl font-bold">Atommodell nach Rutherford</h1>
                <p class="text-2xl">
                    <span class="text-red-500">Ernest Rutherford</span> war ein englischer Physiker, der im 19. <br/>
                    Jahrhundert lebte. 
                    Er führte ein Experiment durch, bei dem er <br/><span class="text-red-500">Alpha-Strahlen</span> auf
                    eine Goldfolie schoss. <br/>
                    Dabei stellte er sich das Atom als <span class="text-red-500">positiv geladenen</span> Kern vor,
                    <br/>
                    um den die <span class="text-red-500">Elektronen</span> kreisen.
                </p>
                <p class="text-2xl mt-20 opacity-0 transition-opacity" class:opacity-100={scroll > 4}>
                    Die Elektronen sind viel <span class="text-red-500">kleiner</span> als der Kern. <br/>
                    Der Kern ist <span class="text-red-500">positiv geladen</span>, <br/>
                    die Elektronen sind <span class="text-red-500">negativ geladen</span>.
                </p>
            </div>
        </div>
    </div>


    <div class="hero min-h-screen fixed justify-end"
         style="top: {bohrTextTop(scroll)}vh; opacity: {bohrTextOpacity(scroll)}">
        <div class="hero-content flex-col text-right">
            <div>
                <h1 class="text-5xl font-bold">Atommodell nach Bohr</h1>
                <p class="text-2xl">
                    <span class="text-red-500">Niels Bohr</span> war ein dänischer Physiker, der im 19. Jahrhundert
                    lebte. <br/>
                    Er stellte sich das Atom als <span class="text-red-500">positiv geladenen</span> Kern vor,<br />
                    um den die <span class="text-red-500">Elektronen</span> kreisen. <br/>
                    Die Elektronen können sich nur auf <br />bestimmten <span class="text-red-500">Energieniveaus</span>
                    (Schalen) aufhalten.
                </p>
                <p class="text-2xl mt-20 opacity-0 transition-opacity" class:opacity-100={scroll > 8}>
                    Die Elektronen können <span class="text-red-500">Energie aufnehmen</span> und <br/>
                    auf ein <span class="text-red-500">höheres Energieniveau</span> wechseln. <br/>
                    Sie können auch <span class="text-red-500">Energie abgeben</span> und <br/>
                    auf ein <span class="text-red-500">niedrigeres Energieniveau</span> wechseln.
                </p>
            </div>
        </div>
    </div>
    <div class="hero min-h-screen fixed justify-end"
         style="top: {schroedingerTextTop(scroll)}vh; opacity: {schroedingerTextOpacity(scroll)}">
        <div class="hero-content flex-col text-right">
            <div>
                <h1 class="text-5xl font-bold">Das Orbitalmodell</h1>
                <p class="text-2xl">
                    Das Orbitalmodell ist das <span class="text-red-500">aktuelle Atommodell</span>. <br/>
                    Es wurde von <span class="text-red-500">Erwin Schrödinger</span> und <br/><span
                        class="text-red-500">Werner Heisenberg</span> entwickelt
                    und ist ein <span class="text-red-500">Wahrscheinlichkeitsmodell</span>.<br/>
                    Die Elektronen können sich nur auf bestimmten <br /> <span class="text-red-500">Energieniveaus</span>
                    namens <span class="text-red-500">Orbitale</span> aufhalten.

                </p>
                <p class="text-2xl mt-20 opacity-0 transition-opacity" class:opacity-100={scroll > 10}>
                    Wahrscheinlichkeitsmodell bedeutet, dass die Elektronen <br/>
                    nicht auf einer festen Bahn um den Kern kreisen, <br/>
                    sondern sich in einem <span class="text-red-500">Wahrscheinlichkeitsraum</span> aufhalten.
                </p>
            </div>
        </div>
    </div>
</div>

{#if scroll === 0}
    <div class="fixed top-0 bottom-0 z-[99999] left-0 right-0 flex justify-center items-center" transition:fade>
        <h1 class="text-5xl font-bold text-slate-400 text-opacity-40">Please scroll</h1>
    </div>
{:else}
    <div class="fixed top-0 bottom-0 z-[99999] left-0 right-0 flex justify-start items-center" transition:fade>
        <ul class="steps steps-vertical ml-3">
            <li class="step" class:step-info={isActiveDemokrit(scroll)}>Demokrit</li>
            <li class="step" class:step-info={isActiveDalton(scroll)}>Dalton</li>
            <li class="step" class:step-info={isActiveThomson(scroll)}>Thomson</li>
            <li class="step" class:step-info={isActiveRutherford(scroll)}>Rutherford</li>
            <li class="step" class:step-info={isActiveBohr(scroll)}>Bohr</li>
            <li class="step" class:step-info={isActiveSchroedinger(scroll)}>Orbitalmodell</li>
        </ul>
    </div>

    <div class="fixed top-0 bottom-0 z-[99999] left-0 right-0 flex justify-center items-end" transition:fade>
        <span class="text-3xl text-slate-400 mb-2 text-opacity-40 relative">
            {#if isActiveSchroedinger(scroll)}
                <span>1928</span>
            {:else if isActiveBohr(scroll)}
                <span>1915</span>
            {:else if isActiveRutherford(scroll)}
                <span>1909-1911</span>
            {:else if isActiveThomson(scroll)}
                <span>1903</span>
            {:else if isActiveDalton(scroll)}
                <span>1808</span>
            {:else if isActiveDemokrit(scroll)}
                <span>5. Jh. v. Chr.</span>
            {/if}
        </span>
    </div>
{/if}

<style>
    /* lh -> up the text a little */
    .tup {
        line-height: 0.8;
    }

    .tag {
        clip-path: polygon(7% 74%, 18% 86%, 52% 50%, 100% 50%, 100% 34%, 45% 34%);
    }

    .theshadow {/*large #1d232a shadow*/
        text-shadow: 0 0 0.5rem #1d232a, 0 0 1rem #1d232a, 0 0 2rem #1d232a, 0 0 4rem #1d232a, 0 0 8rem #1d232a, 0 0 16rem #1d232a;
    }

    .after-name::after {
        content: "H - 1s-Orbital";
        transform: translate(0, 200px);
        font-size: 1.5rem;
        color: #ffffff;
        text-shadow: 0 0 0.5rem #1d232a, 0 0 1rem #1d232a, 0 0 2rem #1d232a, 0 0 4rem #1d232a, 0 0 8rem #1d232a, 0 0 16rem #1d232a;
    }
</style>