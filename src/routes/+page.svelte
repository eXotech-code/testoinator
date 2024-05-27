<script lang="ts">
	import { goto, afterNavigate } from '$app/navigation';

	import questions from '$lib/questions.json';

	type Question = (typeof questions)[0];

	const randomInt = (max: number): number => Math.floor(Math.random() * max);

	const shuffle = (current: Question): Question => {
		let left = current.answers.map((answer, i) => ({
			correct: current.correct.includes(i),
			answer
		}));
		current.correct = [];
		for (let i = 0; i < current.answers.length; i++) {
			const choice = left.splice(randomInt(left.length - 1), 1)[0];
			current.answers[i] = choice.answer;
			if (choice.correct) current.correct.push(i);
		}

		return current;
	};

	const submitAnswer = () => {
		if (current.correct.every((val) => selectedAnswers[val])) {
			if (left.length === 0) {
				goto('/win');
				return;
			}
			selectedAnswers = Array(4).fill(false);
			current = left.splice(randomInt(left.length - 1), 1)[0];
			current = shuffle(current);
		}
	};

	const select = (i: number) => {
		selectedAnswers[i] = !selectedAnswers[i];
		if (current.correct.length === 1) selectedAnswers = selectedAnswers.map((_, j) => i === j);
	};

	let left = questions;
	let current = left.splice(randomInt(questions.length - 1), 1)[0];
	let selectedAnswers: boolean[] = Array(4).fill(false);

	afterNavigate(() => (left = questions));
</script>

<main>
	<h2>{current.question}</h2>
	{#if current.correct.length === 1}
		<p>Jest jedna poprawna odpowiedź.</p>
	{:else}
		<p>Są {current.correct.length} poprawne odpowiedzi.</p>
	{/if}
	<div>
		{#each current.answers as answer, i}
			<button
				class={`selectable ${selectedAnswers[i] ? 'selected' : ''}`}
				on:click={() => select(i)}
			>
				{answer}
			</button>
		{/each}
		<button class="next" on:click={submitAnswer}>{left.length === 0 ? 'Koniec' : 'Dalej'}</button>
	</div>
</main>

<style>
	@import url('https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap');

	:global(body) {
		font-family: 'Roboto', sans-serif;
	}

	main {
		display: flex;
		flex-direction: column;
		gap: 1rem;
		align-items: center;
		justify-content: center;
		padding: 0 2rem;
	}

	p {
		font-size: 14px;
	}

	div {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		gap: 0.5rem;
		width: 20rem;
	}

	button {
		border: 1px solid #000;
		border-radius: 4px;
		padding: 0.25rem;
		width: 100%;
	}
	.selectable {
		background: #fff;
	}

	.selected,
	.next {
		background: #000;
		color: #fff;
	}
</style>
