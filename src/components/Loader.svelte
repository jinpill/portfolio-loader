<script lang="ts">
    import { onDestroy } from "svelte";
    import { fade } from "svelte/transition";

    type Lines = number[];
    type Inners = Lines[];
    type Outers = Inners[];

    const MAXIMUM_OUTER_COUNT = 2;
    const LINE_COUNTS = [2, 3, 4];
    const outers: Outers = [];

    let visible = false;

    /**
     * The name of this method is "appear".
     * 
     * But it's not involved in the fade-in animation.
     * 
     * It only set contents of lines to show.
     */
    const appear = () => {
        visible = true;
        LINE_COUNTS.sort(() => 1 - Math.random() * 2);

        for (let i = 0; i < MAXIMUM_OUTER_COUNT; i++) {
            const inners: Inners = [];
            const innersCount = LINE_COUNTS[i];

            for (let j = 0; j < innersCount; j++) {
                const lines: Lines = [];

                const getRandomRangeNumber = (minimum: number, maximum: number): number => {
                    const random = Math.random() * (maximum - minimum + 1);
                    return Math.floor(random) + minimum;
                }

                const linesCount = getRandomRangeNumber(2, 5);

                for (let k = 0; k < linesCount; k++) {
                    const lineLength = getRandomRangeNumber(2, 6);
                    lines.push(Math.ceil(lineLength));
                }

                inners.push(lines);
            }

            outers.push(inners);
        }
    }

    /**
     * The name of this method is "disappear".
     * 
     * But it's not involved in the fade-out animation.
     * 
     * It only reset the outer's contents.
     */
    const disappear = () => {
        visible = false;
        outers.length = 0;
    }

    // Make the line appear gradually.
    const fadeIn = (node: Element, {
        delay = 0,
        duration = 400
    }) => {
        const computedStyle = getComputedStyle(node);
        const o = Number(computedStyle.opacity);
        const w = Number(computedStyle.width.replace("px", ""));

        return {
            delay,
            duration,
            css: (t: number) => `
                width: ${24 + t * (w - 24)}px;
                opacity: ${t * o};
            `
        };
    }

    // Make the line disappear gradually.
    const fadeOut = fade;

    // Update count of dots.
    let dots: number = 0;

    const updateDots = () => {
        const MAX_COUNT = 3;
        dots = dots < MAX_COUNT ? dots + 1 : 0;
    }

    const timeouts: NodeJS.Timeout[] = [];
    const timers: NodeJS.Timer[] = [];

    // Register animations.
    timeouts[0] = setTimeout(() => {
        const APPEARANCE_DELAY = 1600;
        const DISAPPEARANCE_DELAY = 3000;

        // The first appearance.
        appear();
        timeouts[1] = setTimeout(disappear, DISAPPEARANCE_DELAY);

        // Repeat appearing and disappearing.
        timers[0] = setInterval(appear, APPEARANCE_DELAY + DISAPPEARANCE_DELAY);
        timeouts[2] = setTimeout(() => timers[1] = setInterval(disappear, APPEARANCE_DELAY + DISAPPEARANCE_DELAY), DISAPPEARANCE_DELAY);

        const UPDATE_DOTS_DELAY = 1000;

        // Update count of dots.
        updateDots();
        timers[2] = setInterval(updateDots, UPDATE_DOTS_DELAY);
    }, 500);



    // If this page doesn't finish, show the reloader.
    const WAITING_LIMITATION = 5 * 1000;
    let isInfinite = false;

    const reload = () => {
        window.location.reload();
    }

    timeouts[3] = setTimeout(() => {
        isInfinite = true;
    }, WAITING_LIMITATION);



    // To clear timeouts and intervals.
    onDestroy(() => {
        for (let i = 0; i < timeouts.length; i++) {
            const timeout = timeouts[i];
            clearTimeout(timeout);
        }

        for (let i = 0; i < timers.length; i++) {
            const timer = timers[i];
            clearInterval(timer);
        }
    });
</script>



<div class="loader container">
    <p class="description">
        내용을 불러오는 중이에요{".".repeat(dots)}
    </p>
    <ul class="outers">
        {#if visible}
            {#each outers as inners, i}
                <li>
                    <ul class="inners">
                        {#each inners as lines, j}
                            <li>
                                <ul class="lines">
                                    {#each lines as line, k}
                                        {@const fadeInDelay = i * 100 + j * 200 + k * 300}
                                        {@const fadeOutDelay = fadeInDelay / 2}

                                        <li
                                            style:width="{line * 24}px"

                                            in:fadeIn={{
                                                delay: fadeInDelay,
                                                duration: line * 50
                                            }}

                                            out:fadeOut={{
                                                delay: fadeOutDelay,
                                                duration: 300
                                            }}
                                        ></li>
                                    {/each}
                                </ul>
                            </li>
                        {/each}
                    </ul>
                </li>
            {/each}
        {/if}
    </ul>
    {#if isInfinite}
        <div class="reloader" in:fade={{ duration: 500 }}>
            <p>
                화면이 바뀌지 않아요!
            </p>
            <button on:click={reload}>
                화면 새로고침
            </button>
        </div>
    {/if}
</div>



<style lang="scss">
    ul {
        margin: 0;
        padding-left: 0;
        list-style: none;
    }

    p {
        margin: 0;
    }

    button {
        cursor: pointer;
    }

    .container {
        height: 100%;

        display: flex;
        flex-direction: column;

        overflow-y: auto;
    }

    .description {
        flex: none;

        color: #acacac;
        font-size: 32px;
        user-select: none;

        @media all and (max-width: 480px) {
            & {
                font-size: 24px;
            }
        }
    }

    .outers {
        flex: 0 1 440px;
        margin-top: 64px;
        overflow: hidden;

        @keyframes opacity {
            0% {
                opacity: 1;
            }

            50% {
                opacity: 0.75;
            }

            100% {
                opacity: 1;
            }
        }

        animation: opacity 2s infinite;

        > li {
            &:not(:first-child) {
                margin-top: 96px;
            }
        }
    }

    .inners {
        display: flex;
        flex-direction: column;

        > li {
            &:not(:first-child) {
                margin-top: 24px;
            }
        }
    }

    .lines {
        display: flex;
        flex-direction: row;

        > li {
            height: 24px;

            flex: none;

            border-radius: 12px;
            background-color: black;

            opacity: 0.1;

            &:not(:first-child) {
                margin-left: 24px;
            }
        }
    }

    .reloader {
        flex: none;
        display: flex;
        flex-direction: column;
        align-items: center;
        margin-top: 24px;

        p {
            color: #808080;
            font-size: 16px;
            text-align: center;
            user-select: none;
        }

        button {
            display: block;
            margin-top: 8px;
            padding: 4px 12px;

            border-radius: 3px;
            border: 1px solid dodgerblue;
            background: dodgerblue;

            color: white;
            font-size: 16px;

            &:hover {
                filter: brightness(0.95);
                transition: filter 0.2s;
            }
        }
    }
</style>