<script lang="ts">
	import { onMount } from 'svelte';
	import '../app.css';

	let fileContent: string[] = [];
	let scrambled: string[] = [];
	let answerLength: string[] = [];
	let solve: string = '';
	let solution: string = '';

	onMount(async () => {
		try {
			const response: Response = await fetch('src/data/words.json');
			if (!response.ok) {
				throw new Error(`Response status: ${response.status}`);
			}
			fileContent = await response.json();
		} catch (error) {
			console.error(error);
		}
		let random: number = Math.floor(Math.random() * fileContent.length);
		let word: string = fileContent[random];
		let wordArray: string[] = word.split('');

		for (let i = 0; i < word.length; i++) {
			let next: number = Math.floor(Math.random() * wordArray.length);
			scrambled.push(wordArray[next]);
			wordArray.splice(next, 1);
		}
		solve = scrambled.join('');
		solution = word;
		answerLength.length = scrambled.length;
	});
	let answer: string[] = [];
	const handleInput = (e: KeyboardEvent) => {
        const inputElement = e.target as HTMLInputElement;
		const active: HTMLElement = document.activeElement as HTMLElement;
		if (e.key !== 'Backspace') {
			answer.push(inputElement.value);
			console.log(answer.length);
			if (active.nextElementSibling) {
				(active.nextElementSibling as HTMLInputElement).focus();
			}
		} else if (e.key === 'Backspace') {
			answer.pop();
            inputElement.value == "";
			console.log(answer.length);
			if (active.previousElementSibling) {
				(active.previousElementSibling as HTMLInputElement).focus();
			}
		}
		if (answer.length == answerLength.length) {
			if (answer.join('') == solution) {
				const winner = document.getElementById('won') as HTMLElement;
				winner.style.display = 'block';
			} else {
				const loser = document.getElementById('lost') as HTMLElement;
				loser.style.display = 'block';
				setTimeout(() => {
					loser.style.display = 'none';
				}, 5000);
			}
		}
	};
</script>

<section class="container mx-auto text-center">
	<div class="py-3">
		<h1 class="text-7xl font-bold">Word Scrambler 2.0</h1>
		<p class="text-3xl">Directions: Descramble the word.</p>
	</div>
	<div class="py-3">
		<h1 class="text-5xl">{solve}</h1>
	</div>
	<div class="flex flex-row flex-wrap justify-center gap-3">
		{#each answerLength as item, index}
			<input
				id={`${index}`}
				class="border-2 outline outline-1 w-14 h-20 shadow-lg text-4xl text-center"
				bind:value={item}
				on:keydown={handleInput}
				maxlength="1"
			/>
		{/each}
	</div>
</section>
<section id="won" class="text-center hidden">
	<div>
		<h1>Congratulation, you solved it!.</h1>
	</div>
	<div>
		<a href="/" class="border-2 border-slate-700 p-2" data-sveltekit-reload>Play Again</a>
	</div>
</section>
<section id="lost" class="text-center hidden">
	<div>
		<h1>Sorry, try again.</h1>
	</div>
</section>
