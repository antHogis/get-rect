<script lang="ts">
    import type { Point, Rectangle } from 'src/types';

    let origin: Point | undefined;
    let current: Point | undefined;
    let rectangle: Rectangle | undefined;

    let canvas: HTMLCanvasElement;
    let rectangleContext: CanvasRenderingContext2D;

    function getDrawnRectangle(a: Point, b: Point): Rectangle {
        return {
            x: a.x < b.x ? a.x : b.x,
            y: a.y < b.y ? a.y : b.y,
            width: Math.abs(a.x - b.x),
            height: Math.abs(a.y - b.y),
        };
    }

    function onDrawCurrent(e: MouseEvent) {
        if (canvas) {
            const rect = canvas.getBoundingClientRect();
            current = {
                x: e.clientX - rect.x,
                y: e.clientY - rect.y,
            };
        }
    }

    function onDrawBegin(e: MouseEvent) {
        if (canvas) {
            const rect = canvas.getBoundingClientRect();
            current = origin = {
                x: e.clientX - rect.x,
                y: e.clientY - rect.y,
            };
        }
    }

    function onDrawEnd() {
        origin = undefined;
    }

    $: if (canvas && origin && current) {
        if (rectangle && rectangleContext) {
            console.count('clear');
            rectangleContext.clearRect(
                rectangle.x,
                rectangle.y,
                rectangle.width,
                rectangle.height
            );
        }

        rectangleContext = canvas.getContext('2d');
        rectangle = getDrawnRectangle(current, origin);
        console.count('draw');
        rectangleContext.strokeRect(
            rectangle.x,
            rectangle.y,
            rectangle.width,
            rectangle.height
        );
    }
    $: if (canvas && rectangleContext && rectangle && !origin) {
        console.count('clear');
        rectangleContext.clearRect(
            rectangle.x,
            rectangle.y,
            rectangle.width,
            rectangle.height
        );
        rectangle = undefined;
    }
</script>

<canvas
    bind:this={canvas}
    on:mousedown={onDrawBegin}
    on:mouseup={onDrawEnd}
    on:mousemove={onDrawCurrent}
    on:mouseleave={onDrawEnd}
    width="600px"
    height="400px"
/>

<style>
    canvas {
        background-color: rgb(255, 255, 255);
        outline: #f66 dashed;
    }
</style>
