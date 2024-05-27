<script lang="ts">
	import Footer from '$lib/components/Footer.svelte';
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

	const chooseQuestion = (left: Question[]): [Question, Question[]] => {
		const choice = randomInt(left.length - 1);
		return [left[choice], left.toSpliced(choice, 1)];
	};

	const submitAnswer = () => {
		if (selectedAnswers.every((val, i) => !val || current.correct.includes(i))) {
			if (left.length === 0) {
				goto('/win');
				return;
			}
			selectedAnswers = Array(4).fill(false);
			[current, left] = chooseQuestion(left);
			current = shuffle(current);
			counter++;
		}
	};

	const select = (i: number) => {
		selectedAnswers[i] = !selectedAnswers[i];
		if (current.correct.length === 1) selectedAnswers = selectedAnswers.map((_, j) => i === j);
	};

	let [current, left] = chooseQuestion(JSON.parse(JSON.stringify(questions)));
	let selectedAnswers: boolean[] = Array(4).fill(false);
	let counter = 1;

	afterNavigate(() => (left = questions));
</script>

<main>
	<h2>{current.question}</h2>
	{#if current.correct.length === 1}
		<p>Jest jedna poprawna odpowiedź.</p>
	{:else}
		<p>Są {current.correct.length} poprawne odpowiedzi.</p>
	{/if}
	<div class="answer-box">
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
	<div class="counter-box">
		<p>{counter}/{questions.length}</p>
	</div>
</main>
<Footer />

<style>
	@import url('https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap');

	:global(body) {
		height: 100%;
		font-family: 'Roboto', sans-serif;
		display: flex;
		flex-direction: column;
		gap: 10rem;
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

	.answer-box {
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

	.counter-box {
		display: flex;
		justify-content: flex-end;
	}
</style>
