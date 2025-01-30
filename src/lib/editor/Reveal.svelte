<!-- Initialize reveal -->
<script lang="ts">
    import { onMount } from "svelte";
    import Reveal from "reveal.js";

    import LayoutMain from "./layouts/Layout-Main.svelte";
    import LayoutBody from "./layouts/Layout-Body.svelte";
    import LayoutColumns from "./layouts/Layout-Columns.svelte";

    import Trash from "../icons/Trash.svelte";

    import {
        RevealInstance,
        revealSlides,
        currentSlideH,
        currentSlideV,
        currentLanguage,
        showOverview,
        darkTheme
    } from "../../stores";
    import { Layouts } from "../../types";
    import Close from "../icons/Close.svelte";
    import { Slide } from "../../classes/Slide";

    /**
     * Delete the current slide from the set of slides
     * @param event event triggering this function
     */
    function removeCurrentSlide(event) {
        // Prevent the event from bubbling up to the parent element
        event.stopPropagation();

        // Store which slide is currently selected
        const toRemoveH = $currentSlideH;
        const toRemoveV = $currentSlideV;

        // Move to the previous slide
        if ($currentSlideV == 0) {
            $RevealInstance.slide($currentSlideH - 1, 0);
        } else {
            $RevealInstance.slide($currentSlideH, $currentSlideV - 1);
        }

        // Remove the slide the user requested for removal
        revealSlides.update((slides) => {
            slides[toRemoveH].splice(toRemoveV, 1);

            if (slides[toRemoveH].length == 0) {
                slides.splice(toRemoveH, 1);
            }

            return slides;
        });

        // Synchronize the Reveal instance
        setTimeout(() => {
            $RevealInstance.sync();
        }, 100);
    }

    /**
     * On mount initialize Revealjs
     */
    onMount(() => {
        Reveal.initialize({
            // Display controls in the bottom right corner
            controls: true,
            // Display a presentation progress bar
            progress: true,
            // Push each slide change to the browser history
            history: false,
            // Enable keyboard shortcuts for navigation
            keyboard: true,
            // Enable the slide overview mode
            overview: false,
            // Vertical centering of slides
            center: true,
            // Should a help overlay be presented when the question mark key is pressed?
            help: false,
            // Should it be possible to pause the presentation?
            pause: false,
            // Loop the presentation
            loop: false,
            // Enables touch navigation on devices with touch input
            touch: true,
            // Apply a 3D roll to links on hover
            rollingLinks: true,
            // Transition style for full page slide backgrounds
            backgroundTransition: "fade", // none/fade/slide/convex/concave/zoom
            // Select a theme
            theme: $darkTheme ? "blood" : "white",
            // Select the type of slides transition
            transition: "slide",
            embedded: false,
            // IMPORTANT: disable the default layout (centering and scaling) to make the code editors work correctly
            disableLayout: true,
        }).then(() => {
            // When the slide changes, update the indices in the store
            Reveal.addEventListener("slidechanged", (e) => {
                $currentSlideH = e.indexh;
                $currentSlideV = e.indexv;
            });

            // Save the Revealjs instance in the store
            $RevealInstance = Reveal;
        });
    });

    /**
     * Add a new slide to the presentation
     */
    const newSlide = () => {
        $revealSlides = [
            ...$revealSlides,
            [
                new Slide(
                    $revealSlides.length,
                    0,
                    $currentLanguage,
                    Layouts.BODY
                ),
            ],
        ];
        $RevealInstance.sync();
        setTimeout(() => {
            $RevealInstance.slide($revealSlides.length - 1, 0);
        }, 50);
    };

    /**
     * Add a new vertical slide to the presentation
     */
    const newVerticalSlide = () => {
        const newSlides = [...$revealSlides];
        newSlides[$currentSlideH].push(
            new Slide(
                $currentSlideH,
                newSlides[$currentSlideH].length,
                $currentLanguage,
                Layouts.BODY
            )
        );
        $revealSlides = newSlides;
        $RevealInstance.sync();
        setTimeout(() => {
            $RevealInstance.slide(
                $currentSlideH,
                newSlides[$currentSlideH].length - 1
            );
        }, 50);
    };

    const javaCode = `public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!"); 
    }
}`;
</script>

<div class="absolute top-[-25px] right-[calc(50%-60px)] btn-shadow w-[120px] h-[50px] rounded-lg" on:click={() => {revealSlides.set([...$revealSlides]);
    showOverview.set(true);}}>
    Overview
</div>
<div class="absolute right-[-25px] top-[calc(50%-25px)] btn-shadow w-[50px] h-[50px]" on:click={newSlide}>
    <Close customClass="rotate-45" color="#dfdfdf" />
</div>
<div class="absolute bottom-[-25px] right-[calc(50%-25px)] rounded-full bg-primary btn-shadow w-[50px] h-[50px] z-50" on:click={newVerticalSlide}>
    <Close customClass="rotate-45" color="#dfdfdf" />
</div>
<div
    class="h-screen full-page-demo reveal-viewport"
    data-page="icp"
    style="box-sizing: border-box; width: 100%; height: 100%; transition: transform 0.8s ease 0s;"
>
    <div
        class="transition-all absolute top-0 right-0 p-4 z-40 {$currentSlideH ==
        0
            ? 'opacity-0'
            : 'opacity-100'}"
        on:click={removeCurrentSlide}
    >
        <Trash customClass="cursor-pointer scale-75" />
    </div>
    <div
        class="reveal slide focused has-horizontal-slides ready"
        role="application"
        data-transition-speed="default"
        data-background-transition="fade"
    >
        <!-- ADD THE DEMO HERE -->
        <div class="slides">
            <section>
                <!-- title class: custom style for titles -->
                <h3 class="title">Interactive Code Playgrounds</h3>

                <!-- subtitle class: custom style for subtitles -->
                <p class="subtitle">Rethinking the user experience of slideshows in programming classes</p>
                <div>
                    <p class="xsmall">An interactive demo of the <a href="https://github.com/lucademenego99/icp-bundle"
                            target="_blank">Interactive Code
                            Playgrounds</a> project</p>
                    <div class="row mt2" style="gap: min(0.6vw, 1.2vh); align-items: center">
                        <p class="xsmall bold">Recommended browser:</p>
                        <a href="https://www.google.com/intl/it_it/chrome/" target="_blank" style="display: flex;">
                            <svg xmlns="http://www.w3.org/2000/svg"
                                style="width: min(1.5vw, 3vh); height: min(1.5vw, 3vh);" viewBox="0 0 190.5 190.5"
                                xmlns:v="https://vecta.io/nano">
                                <g transform="translate(90.669 -507.469)">
                                    <path
                                        d="M4.583 650.342c26.304 0 47.627-21.324 47.627-47.628s-21.323-47.628-47.627-47.628-47.627 21.324-47.627 47.628 21.323 47.628 47.627 47.628z"
                                        fill="#fff" clip-path="none" mask="none" />
                                    <path
                                        d="M-36.664 626.539l-41.24-71.43c-8.362 14.479-12.765 30.904-12.765 47.625s4.401 33.146 12.762 47.625 20.387 26.503 34.868 34.86 30.908 12.755 47.628 12.75l41.24-71.43v-.011c-4.177 7.244-10.188 13.26-17.428 17.443a47.62 47.62 0 0 1-47.632.007 47.62 47.62 0 0 1-17.433-17.437z"
                                        fill="#229342" clip-path="none" mask="none" />
                                    <path
                                        d="M45.826 626.536l-41.239 71.43c16.72.003 33.146-4.398 47.626-12.757s26.504-20.384 34.863-34.865a95.24 95.24 0 0 0 12.755-47.627c-.003-16.72-4.408-33.145-12.772-47.623H4.58l-.01.007a47.62 47.62 0 0 1 23.819 6.372c7.243 4.179 13.257 10.19 17.439 17.431a47.62 47.62 0 0 1-.001 47.633z"
                                        fill="#fbc116" clip-path="none" mask="none" />
                                    <path
                                        d="M4.583 640.43c20.824 0 37.705-16.881 37.705-37.706s-16.881-37.705-37.705-37.705-37.705 16.881-37.705 37.705 16.881 37.706 37.705 37.706z"
                                        fill="#1a73e8" clip-path="none" mask="none" />
                                    <path
                                        d="M4.583 555.097h82.479c-8.358-14.481-20.381-26.507-34.861-34.868a95.23 95.23 0 0 0-47.625-12.76c-16.72.001-33.145 4.404-47.623 12.767a95.23 95.23 0 0 0-34.856 34.872l41.24 71.43.011.006a47.62 47.62 0 0 1-.015-47.633c4.179-7.242 10.193-13.256 17.434-17.436s15.456-6.381 23.818-6.379z"
                                        fill="#e33b2e" clip-path="none" mask="none" />
                                </g>
                            </svg>
                        </a>
                    </div>
                </div>
            </section>

            <section>
                <div class="row">
                    <div class="col" style="margin-left: 2vw;">
                        <p>A typical slide in a programming class looks like this:</p>

                        <p>A static image from which you can't select text and if you manage to copy and paste it, the output might be unexpected.</p>

                        <p>Not only this makes the lesson less engaging, but also as a teacher, if you need to change something, you need to re-make the image.</p>
                    </div>

                    <img src="assets/codeSample.png"
                        alt="Sample code snippet showing basic coding concepts"
                        style="max-width: 50%; object-fit: contain; max-height: 80vh; margin: min(1vw, 2vh);" />
                </div>
            </section>

            <section>
                <div class="row">
                    <div class="col">
                        <ul style="list-style-type: none;">
                            <li>But the biggest problem is this:</li>
                            <li class="fragment">Are you studying, and curious to know what this bit of code does?</li>
                        </ul>
                        <div class="fragment">
                            <h4>Tough <span class="fragment">luck.</span></h4>
                        </div>
                    </div>

                    <img src="assets/codeSample.png"
                        alt="Sample code snippet showing basic coding concepts"
                        style="max-width: 50%; object-fit: contain; max-height: 80vh; margin: min(1vw, 2vh);" />
                </div>
            </section>

            <section>
                <section>
                    <p class="big">Okay, that was maybe a bit excessive.</p>
                    <p>You can actually do that, but it'll take jumping through a few hoops. <br />
                        Find out by going down, or just skip ahead to the right.</p>
                </section>
                <section>
                    <p>
                        Alright, there was some interesting code on that slide! <br />
                        Let's assume you want to run a bit of Python code...
                    </p>
                </section>
                <section>
                    <p>
                        You'll just need to...
                    </p>
                </section>
                <section>
                    <p>Open the Python interpreter</p>
                    <p class="fragment">Open a text editor</p>
                    <p class="fragment">Select the code on the slide (if you can!)</p>
                    <p class="fragment">Copy and paste it in the text editor (or transcribe it manually!)</p>
                    <p class="fragment">Check for copy/paste mistakes</p>
                    <p class="fragment">Run the code in the interpreter</p>
                    <p class="fragment">Whew!</p>
                </section>
                <section>
                    <p>Can't we do better?</p>
                </section>
            </section>

            <section>
                <!-- row class: custom class to set up a two-columns layout - you need to use row + two columns -->
                <div class="row" style="width: 100%">
                    <!-- col class: first column of the row defined before -->
                    <div class="col" style="text-align: left; margin-left: min(2vw, 4vh)">
                        <p class="fragment semi-fade-out big" data-fragment-index="1">What if code on slides could look
                            like...</p>
                        <div class="fragment" data-fragment-index="2">
                            <h4>this?</h4>
                        </div>
                        <p class="fragment" data-fragment-index="4">Go ahead and try clicking that "Run Code!" button.
                        </p>
                        <p class="fragment" data-fragment-index="5">Did you see what happened in the "Output" box?</p>
                        <p class="fragment" data-fragment-index="6">The code was run <span class="emph">directly on the
                                slide</span>!</p>
                        <p class="fragment" data-fragment-index="7">What's more, you can edit the code directly in the
                            text box!</p>
                        <p class="fragment" data-fragment-index="8">Why don't you make some changes, run the code again,
                            and see what happens?</p>
                        <p class="fragment" data-fragment-index="9">Move on when you're done playing.</p>
                    </div>
                    <!-- col class: second column of the row defined before -->
                    <!-- Note: the editor takes the size of the parent, so it must be specified -->
                    <!--    in this case the parent is 100% width, 60vh height -->
                    <div class="col fragment" data-fragment-index="2"
                        style="height: min(30vw, 60vh); margin: min(1vw, 2vh)">
                        <!-- Normal editor, set the language and the code inside it -->
                        <!--    to create editable parts, just use <EDITABLE>...</EDITABLE> -->
                        <!--    for more info: https://github.com/lucademenego99/icp-bundle -->
                        <!-- Note: for reveal.js to work we need to set content editable to true -->
                        <python-editor contenteditable="true" theme="dark" code="elements = [39,12,18,85,72,10,2,18]

print('Unsorted list is\n', elements)

# Sort the list (with bubblesort)
for n in range(len(elements)-1, 0, -1):
for i in range(n):
    if elements[i] > elements[i+1]:
        elements[i], elements[i+1] = elements[i+1], elements[i]

print('\nSorted list is\n', elements)" />
                    </div>
            </section>

            <section>
                <p>The benefits of the usage of interactive code editors during a lesson should be ovious at this point, but...</p>

                <p class="fragment" style="margin-left: 8vw; margin-right: 8vw;">...what are the costs for a teacher? How <strong>difficult</strong> is it to create these interactive slides compared to what we are already used to?</p>
            </section>

            <section>
                <section>
                    <p>It is incredibly <strong>EASY!</strong></p>

                    <p class="fragment">What you are seeing at the moment is exactly an editor to create ICP slide decks.</p>

                    <p class="fragment">Below you will find a quick tutorial about its usage or move right to start playing with the editor.</p>
                </section>

                <section>
                    <p>The <strong>+</strong> buttons on the right and at the bottom are used to create, respectively, new horizontal and vertical slides.</p>
                </section>  

                <section>
                    <div class="row">
                        <div class="col">
                            <p>The <strong>Selected Layout</strong> button can be used to modify the layout of the current slide.</p>
                        </div>
    
                        <img src="assets/selectedLayout.png"
                            alt="Sample code snippet showing basic coding concepts"
                            style="max-width: 50%; object-fit: contain; max-height: 80vh; margin: min(1vw, 2vh);" />
                    </div>
                </section>

                <section>
                    <div class="row">
                        <div class="col">
                            <p>If the current slide layout allows to add a code editor, just press the corresponding button to include it!</p>

                            <p>The <strong>Selected language</strong> allows instead to select the language of the code editor in the current slide.</p>
                        </div>
    
                        <img src="assets/selectedLayout.png"
                            alt="Sample code snippet showing basic coding concepts"
                            style="max-width: 50%; object-fit: contain; max-height: 80vh; margin: min(1vw, 2vh);" />
                    </div>
                </section>
            </section>

            {#each $revealSlides as verticalSlides, index (index)}
                <section>
                    {#each verticalSlides as slide (slide.id)}
                        {#if slide.layout == Layouts.BODY}
                            <LayoutBody bind:slide />
                        {:else if slide.layout == Layouts.MAIN}
                            <LayoutMain bind:slide />
                        {:else if slide.layout == Layouts.COLUMNS}
                            <LayoutColumns bind:slide />
                        {/if}
                    {/each}
                </section>
            {/each}
        </div>
    </div>
</div>
