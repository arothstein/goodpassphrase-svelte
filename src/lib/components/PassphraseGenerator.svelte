<script lang="ts">
    import { onMount } from "svelte";
    import { Slider, Checkbox, Button, Label } from "bits-ui";
    import { Check } from "lucide-svelte";
    import words from "$lib/words.js";

    // State using runes
    let ppOptions = $state({
        wordCount: 5,
        addInt: true,
        addSpecial: false,
        ppCount: 5,
    });

    let ppResults = $state({
        Passphrases: [] as string[],
    });

    // Derived values (computed)
    const disableReset = $derived(
        ppOptions.wordCount === 5 &&
            ppOptions.addInt === true &&
            ppOptions.addSpecial === false &&
            ppOptions.ppCount === 5,
    );

    const avgTimeToGuess = $derived.by(() => {
        const { wordCount, addInt, addSpecial } = ppOptions;
        if (wordCount === 2) return "0 seconds";
        if (wordCount === 3) {
            if (!addInt && !addSpecial) return "2 seconds";
            if (addInt && !addSpecial) return "23 seconds";
            if (!addInt && addSpecial) return "47 seconds";
            return "8 minutes";
        }
        if (wordCount === 4) {
            if (!addInt && !addSpecial) return "5 hours";
            if (addInt && !addSpecial) return "2 days";
            if (!addInt && addSpecial) return "4 days";
            return "42 days";
        }
        if (wordCount === 5) {
            if (!addInt && !addSpecial) return "4.5 years";
            if (addInt && !addSpecial) return "45 years";
            if (!addInt && addSpecial) return "90 years";
            return "901 years";
        }
        if (wordCount === 6) {
            if (!addInt && !addSpecial) return "35,051 years";
            if (addInt && !addSpecial) return "350,510 years";
            if (!addInt && addSpecial) return "701,020 years";
            return "7,010,208 years";
        }
        if (wordCount === 7) {
            if (!addInt && !addSpecial) return "272,556,887 years";
            if (addInt && !addSpecial) return "2,725,568,873 years";
            if (!addInt && addSpecial) return "5,451,137,747 years";
            return "54,511,377,465 years";
        }
        return "0 seconds";
    });

    // Methods
    function getPassphrases() {
        localStorage.setItem("ppOptions", JSON.stringify(ppOptions));
        ppResults.Passphrases = [];
        for (let i = 0; i < ppOptions.ppCount; i++) {
            const pp = generatePassphrase(
                ppOptions.wordCount,
                ppOptions.addInt,
                ppOptions.addSpecial,
            );
            ppResults.Passphrases.push(pp);
        }
    }

    function generatePassphrase(
        wordCount: number,
        addInt: boolean,
        addSpecial: boolean,
    ) {
        let passPhrase = getWords(wordCount);

        if (addInt) {
            const randomNum = new Uint32Array(1);
            window.crypto.getRandomValues(randomNum);
            passPhrase += randomNum[0] % 10;
        }

        if (addSpecial) {
            const specials = "@#$%^&*-_!+=[]{}|\\:',.?/`~\"();";
            const randomNum = new Uint32Array(1);
            window.crypto.getRandomValues(randomNum);
            passPhrase += specials.charAt(randomNum[0] % 30);
        }

        return passPhrase;
    }

    function getWords(wordCount: number) {
        let wordList = "";
        for (let i = 0; i < wordCount; i++) {
            const n = rollWordNumber();
            const word = (words as any)[n];
            if (word) {
                wordList += word.charAt(0).toUpperCase() + word.slice(1);
            }
        }
        return wordList;
    }

    function rollWordNumber() {
        let wordNumber = "";
        const randomNum = new Uint32Array(1);
        for (let i = 0; i < 5; i++) {
            window.crypto.getRandomValues(randomNum);
            const dieRoll = (randomNum[0] % 6) + 1;
            wordNumber += dieRoll;
        }
        return wordNumber;
    }

    function resetPpOptions() {
        localStorage.removeItem("ppOptions");
        ppOptions.wordCount = 5;
        ppOptions.addInt = true;
        ppOptions.addSpecial = false;
        ppOptions.ppCount = 5;
    }

    onMount(() => {
        const saved = localStorage.getItem("ppOptions");
        if (saved) {
            try {
                const parsed = JSON.parse(saved);
                ppOptions.wordCount = parsed.wordCount ?? 5;
                ppOptions.addInt = parsed.addInt ?? true;
                ppOptions.addSpecial = parsed.addSpecial ?? false;
                ppOptions.ppCount = parsed.ppCount ?? 5;
            } catch (e) {
                console.error("Failed to parse saved options", e);
            }
        }
        // Generate initial passphrases
        getPassphrases();
    });
</script>

<div class="grid grid-cols-1 md:grid-cols-3 gap-6 w-full">
    <!-- Options Card -->
    <div
        class="md:col-span-1 border border-gray-200 rounded-lg overflow-hidden bg-white shadow-sm flex flex-col h-fit"
    >
        <div class="bg-primary text-white px-4 py-3 text-lg font-medium">
            Passphrase options
        </div>
        <div class="p-4 space-y-8">
            <!-- Word Count Slider -->
            <div class="space-y-4">
                <div class="flex justify-between items-center">
                    <span class="text-gray-700 font-medium tracking-tight"
                        >Words</span
                    >
                    <span
                        class="bg-primary/10 text-primary px-2 py-0.5 rounded text-sm font-bold"
                        >{ppOptions.wordCount}</span
                    >
                </div>
                <Slider.Root
                    type="single"
                    bind:value={ppOptions.wordCount}
                    min={2}
                    max={7}
                    step={1}
                    class="relative flex w-full touch-none select-none items-center"
                >
                    <Slider.Range
                        class="absolute h-1.5 rounded-full bg-primary"
                    />
                    <span
                        class="relative h-1.5 w-full grow overflow-hidden rounded-full bg-gray-200"
                    ></span>
                    <Slider.Thumb
                        index={0}
                        class="block h-5 w-5 rounded-full border-2 border-primary bg-white ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50 hover:scale-110 active:scale-95 duration-150 cursor-grab active:cursor-grabbing"
                    />
                </Slider.Root>
            </div>

            <!-- Passphrase Count Slider -->
            <div class="space-y-4">
                <div class="flex justify-between items-center">
                    <span class="text-gray-700 font-medium tracking-tight"
                        >Generated passphrases</span
                    >
                    <span
                        class="bg-primary/10 text-primary px-2 py-0.5 rounded text-sm font-bold"
                        >{ppOptions.ppCount}</span
                    >
                </div>
                <Slider.Root
                    type="single"
                    bind:value={ppOptions.ppCount}
                    min={1}
                    max={10}
                    step={1}
                    class="relative flex w-full touch-none select-none items-center"
                >
                    <Slider.Range
                        class="absolute h-1.5 rounded-full bg-primary"
                    />
                    <span
                        class="relative h-1.5 w-full grow overflow-hidden rounded-full bg-gray-200"
                    ></span>
                    <Slider.Thumb
                        index={0}
                        class="block h-5 w-5 rounded-full border-2 border-primary bg-white ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50 hover:scale-110 active:scale-95 duration-150 cursor-grab active:cursor-grabbing"
                    />
                </Slider.Root>
            </div>

            <!-- Checkboxes -->
            <div class="space-y-4 pt-2">
                <div class="text-gray-600 text-sm font-medium">
                    If required, add a:
                </div>
                <div class="flex flex-wrap gap-x-8 gap-y-4">
                    <div
                        class="flex items-center space-x-3 group cursor-pointer"
                    >
                        <Checkbox.Root
                            id="addInt"
                            bind:checked={ppOptions.addInt}
                            class="peer h-5 w-5 shrink-0 rounded border-2 border-gray-300 ring-offset-background focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:cursor-not-allowed disabled:opacity-50 data-[state=checked]:bg-primary data-[state=checked]:border-primary transition-all group-hover:border-primary/50 flex items-center justify-center text-white"
                        >
                            {#snippet children({ checked })}
                                {#if checked}
                                    <Check size={14} strokeWidth={4} />
                                {/if}
                            {/snippet}
                        </Checkbox.Root>
                        <Label.Root
                            for="addInt"
                            class="text-sm font-medium leading-none peer-disabled:cursor-not-allowed peer-disabled:opacity-70 cursor-pointer"
                        >
                            Number
                        </Label.Root>
                    </div>

                    <div
                        class="flex items-center space-x-3 group cursor-pointer"
                    >
                        <Checkbox.Root
                            id="addSpecial"
                            bind:checked={ppOptions.addSpecial}
                            class="peer h-5 w-5 shrink-0 rounded border-2 border-gray-300 ring-offset-background focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:cursor-not-allowed disabled:opacity-50 data-[state=checked]:bg-primary data-[state=checked]:border-primary transition-all group-hover:border-primary/50 flex items-center justify-center text-white"
                        >
                            {#snippet children({ checked })}
                                {#if checked}
                                    <Check size={14} strokeWidth={4} />
                                {/if}
                            {/snippet}
                        </Checkbox.Root>
                        <Label.Root
                            for="addSpecial"
                            class="text-sm font-medium leading-none peer-disabled:cursor-not-allowed peer-disabled:opacity-70 cursor-pointer"
                        >
                            Special character
                        </Label.Root>
                    </div>
                </div>
            </div>

            <!-- Time to Guess -->
            <div class="pt-4 border-t border-gray-100">
                <div
                    class="text-gray-500 text-xs font-semibold uppercase tracking-wider mb-1"
                >
                    Average time to guess (100 billion guesses/sec)
                </div>
                <div class="text-2xl font-bold text-gray-800">
                    &asymp; {avgTimeToGuess}
                </div>
            </div>
        </div>

        <!-- Actions -->
        <div
            class="p-4 bg-gray-50 flex justify-end gap-3 mt-auto border-t border-gray-200"
        >
            <Button.Root
                onclick={resetPpOptions}
                disabled={disableReset}
                class="px-4 py-2 uppercase text-sm font-bold tracking-wide text-primary rounded hover:bg-primary/5 transition-colors disabled:opacity-50 disabled:hover:bg-transparent"
            >
                Reset
            </Button.Root>
            <Button.Root
                onclick={getPassphrases}
                class="px-5 py-2 uppercase text-sm font-bold tracking-wide bg-primary text-white rounded shadow hover:bg-primary-dark transition-all hover:shadow-md active:scale-95 hover:bg-[#1565C0]"
            >
                Generate
            </Button.Root>
        </div>
    </div>

    <!-- Passphrases Card -->
    <div
        class="md:col-span-2 border border-gray-200 rounded-lg overflow-hidden bg-white shadow-sm flex flex-col"
    >
        <div class="bg-primary text-white px-4 py-3 text-lg font-medium">
            Passphrases
        </div>
        <ul class="divide-y divide-gray-100 overflow-y-auto">
            {#each ppResults.Passphrases as pp}
                <li
                    class="px-6 py-4 text-lg font-mono tracking-tight text-gray-800 break-all select-all hover:bg-gray-50 transition-colors"
                >
                    {pp}
                </li>
            {/each}
        </ul>
    </div>
</div>

<style>
    /* Use the custom theme colors defined in app.css */
    :global(.bg-primary) {
        background-color: var(--color-primary);
    }
    :global(.text-primary) {
        color: var(--color-primary);
    }
    :global(.border-primary) {
        border-color: var(--color-primary);
    }
</style>
