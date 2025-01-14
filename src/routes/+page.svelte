<script lang="ts">
	const dates = [
		{ label: "Doro's Geburtstag", date: '2025-01-15' },
		{ label: "Nora's Geburtstag", date: '2025-03-05' },
		{ label: "Sebastian's Geburtstag", date: '2025-04-12' },
		{ label: "Bene's Geburtstag", date: '2025-10-07' },
		{ label: 'Weihnachten', date: '2025-12-24' }
	];

	let selectedDate = $state(dates[0].date);

	function findClosestDate() {
		const now = new Date().getTime();
		return dates.reduce((closest, current) => {
			const closestDiff = Math.abs(new Date(closest.date).getTime() - now);
			const currentDiff = Math.abs(new Date(current.date).getTime() - now);
			return currentDiff < closestDiff ? current : closest;
		});
	}

	$effect(() => {
		selectedDate = findClosestDate().date;
	});

	function calculateTimeDifference(date: string) {
		if (!date) return null;
		const now = new Date();
		const targetDate = new Date(date);
		const diffInMs = targetDate.getTime() - now.getTime();
		return Math.ceil(diffInMs / (1000 * 60 * 60 * 24));
	}

	let timeDifference = $derived(calculateTimeDifference(selectedDate));
</script>

<svelte:head>
	<title>Wie lange noch bis...</title>
	<meta name="description" content="Wie lange noch bis zu einem bestimmten Datum?" />
	<meta property="og:image" content="https://wie-lange-noch-bis.vercel.app/og-image.png" />
</svelte:head>

<div class="background flex min-h-screen w-full items-center justify-center p-4">
	<div class="card flex w-full max-w-md flex-col items-center">
		<h1 class="heading mb-4">Wie lange noch bis...</h1>

		<select class="select mb-4" bind:value={selectedDate}>
			<option value={null}>Bitte wählen...</option>
			{#each dates as { date, label }}
				<option value={date}>{label}</option>
			{/each}
		</select>

		{#if timeDifference !== null}
			<p class="text-lg font-semibold">
				{timeDifference}
				{timeDifference === 1 ? 'Tag!!!' : 'Tage'}
			</p>
		{/if}
	</div>
</div>

<style>
	.background {
		background: linear-gradient(135deg, #f5f7fa, #c3cfe2);
	}
	.card {
		background: white;
		border-radius: 8px;
		box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
		padding: 2rem;
	}
	.heading {
		font-family: 'Roboto', sans-serif;
		font-size: 2.5rem;
		font-weight: 500;
		color: #333;
	}
	.select {
		appearance: none;
		background: #f0f0f0;
		border: none;
		border-radius: 4px;
		padding: 0.5rem;
		width: 100%;
		cursor: pointer;
	}
</style>
