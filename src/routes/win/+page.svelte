<script>
	import questions from '$lib/questions.json';
</script>

<main>
	<h1>You won you fucken piece of fish</h1>
	<a href="/">Mamo, mogę jeszcze raz?</a>
	<p>Obecnie pula pytań wynosi {questions.length}, a zestawy są dobierane losowo.</p>
</main>

<style>
	main {
		display: flex;
		flex-direction: column;
		gap: 5rem;
		justify-content: center;
		align-items: center;
	}

	a {
		text-decoration: none;
		background: #000;
		color: #fff;
		padding: 0.5rem 1rem;
		border-radius: 4px;
	}
</style>
