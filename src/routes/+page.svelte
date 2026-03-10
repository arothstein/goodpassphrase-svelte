<script lang="ts">
    import PassphraseGenerator from "$lib/components/PassphraseGenerator.svelte";
</script>

<svelte:head>
    <title>GoodPassphrase.com - Random Diceware Passphrase Generator</title>
    <meta
        name="description"
        content="Create passphrases you can remember but are hard for hackers/computers to guess using the Diceware method."
    />
</svelte:head>

<div class="max-w-5xl mx-auto space-y-6">
    <div class="space-y-2">
        <p class="text-xl text-gray-500 font-light leading-relaxed">
            Create passphrases you can remember but are hard for
            hackers/computers to guess.
        </p>
    </div>

    <PassphraseGenerator />
</div>
