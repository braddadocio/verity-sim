<script lang="ts">
    import { SHAPE, SHAPE2D, decompose_shape} from "./shapes";
    import sphereUrl from "../assets/sphere.svg";
    import cubeUrl from "../assets/cube.svg";
    import cylinderUrl from "../assets/cylinder.svg";
    import coneUrl from "../assets/cone.svg";
    import pyramidUrl from "../assets/tetrahedron.svg";
    import prismUrl from "../assets/prism.svg";
    import { createEventDispatcher } from "svelte";
    import Shape2D from "./Shape2D.svelte";

    const dispatcher = createEventDispatcher<{shapedropped: SHAPE2D}>();

    export let shape: SHAPE = SHAPE.SPHERE;
    export let callout: SHAPE2D = SHAPE2D.CIRCLE;
    export let selected: boolean = false;
    export let hints: boolean = false;
    export let compact: boolean = false;
    export let width: number = 64;
    export let height: number = 64;

    let droppable: boolean = false;

    function drop(ev: DragEvent) {
        ev.preventDefault();
        if (ev.dataTransfer == null || ev.dataTransfer.getData("text") == "") return;
        let data = ev.dataTransfer.getData("text") as SHAPE2D;
        droppable = false;
        dispatcher("shapedropped", data);
    }

    function dragover(ev: DragEvent) {
        ev.preventDefault();
        if (ev.dataTransfer == null) return;
        droppable = true;
    }

    function getShapeImageURL(shape: SHAPE): string {
        switch (shape) {
            case SHAPE.SPHERE:
                return sphereUrl;
            case SHAPE.CUBE:
                return cubeUrl;
            case SHAPE.CYLINDER:
                return cylinderUrl;
            case SHAPE.CONE:
                return coneUrl;
            case SHAPE.PYRAMID:
                return pyramidUrl;
            case SHAPE.PRISM:
                return prismUrl;
        }
    }

    let shape_1: SHAPE2D, shape_2: SHAPE2D;
    $: {
        shape_1 = decompose_shape(shape)[0];
        shape_2 = decompose_shape(shape)[1];
    }
</script>

<div on:drop={drop} on:dragover={dragover} on:dragleave={() => droppable = false} role="none" class:drop={droppable} class:selected={selected} class:compact={compact}>
    <img {width} {height} src={getShapeImageURL(shape)} alt="" />
    {#if !compact}
        <p>{shape}</p>
    {/if}
    {#if hints}
        <div class="hints">
            <Shape2D invalid={shape_1 === callout} shape={shape_1} draggable={false} border={false} width={28} height={28} padding={false} />
            <span>+</span>
            <Shape2D invalid={shape_2 === callout} shape={shape_2} draggable={false} border={false} width={28} height={28} padding={false} />
        </div>
    {/if}
</div>

<style>
    .hints {
        display: flex;
        flex-direction: row;
        gap: 0.1em;
        border: none;
        margin: 0px;
    }
    .drop {
        border: 1px solid gold;
    }
    div {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        padding: 1rem;
        border: 1px solid black;
        border-radius: 0.5rem;
        margin: 0.5rem;
    }
    .compact {
        border: none !important;
        padding: 0px !important;
    }

    .selected {
        background: linear-gradient(rgba(2, 0, 36, 0), rgba(29, 29, 36, 0.13725490196078433) 46.64%, rgba(197, 200, 24, 1) 75.21%, rgba(247, 255, 0, 1))
    }
    
    @keyframes background-pulse {
        0% {
            background-position-y: 0;
        }
        50% {
            background-position-y: 100%;
        }
        100% {
            background-position-y: 0;
        } 
    }

    @media (prefers-color-scheme: dark) {
        div {
            border-color: white;
        }
        img {
            filter: invert(1.0);
        }
    }
</style>
