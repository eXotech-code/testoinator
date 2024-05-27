<script lang="ts">
	import { goto } from '$app/navigation';

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

	const getInitial = (questions: Question[]): Question[] => {
		let copy = JSON.parse(JSON.stringify(questions));
		let initial = [];
		for (let i = 0; i < 10; i++) {
			initial.push(copy.splice(randomInt(copy.length - 1), 1)[0]);
		}
		return initial;
	};

	let [current, left] = chooseQuestion(getInitial(questions));
	let selectedAnswers: boolean[] = Array(4).fill(false);
	let counter = 1;
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
		<p>{counter}/{10}</p>
	</div>
</main>

<style>
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
