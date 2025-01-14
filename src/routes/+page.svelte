<script lang="ts">
	const dates = [
		{ label: "Doro's Geburtstag", date: '01-15' },
		{ label: "Nora's Geburtstag", date: '03-05' },
		{ label: "Sebastian's Geburtstag", date: '04-12' },
		{ label: "Bene's Geburtstag", date: '10-07' },
		{ label: 'Weihnachten', date: '12-24' }
	];

	let selectedDate = $state(dates[0].date);

	function getNextOccurrence(monthDay: string): Date {
		const [month, day] = monthDay.split('-').map(Number);
		const now = new Date();
		const thisYear = now.getFullYear();
		const dateThisYear = new Date(thisYear, month - 1, day);

		// If the date has already passed this year, use next year
		return dateThisYear < now ? new Date(thisYear + 1, month - 1, day) : dateThisYear;
	}

	function findClosestDate() {
		const now = new Date().getTime();
		return dates.reduce((closest, current) => {
			const closestDate = getNextOccurrence(closest.date).getTime();
			const currentDate = getNextOccurrence(current.date).getTime();
			return currentDate - now < closestDate - now ? current : closest;
		});
	}

	function calculateTimeDifference(monthDay: string) {
		if (!monthDay) return null;
		const now = new Date();
		const targetDate = getNextOccurrence(monthDay);
		const diffInMs = targetDate.getTime() - now.getTime();
		return Math.ceil(diffInMs / (1000 * 60 * 60 * 24));
	}

	$effect(() => {
		selectedDate = findClosestDate().date;
	});

	let timeDifference = $derived(calculateTimeDifference(selectedDate));
</script>

<svelte:head>
	<title>Wie lange noch bis...</title>
	<meta name="description" content="Wie lange noch bis zu einem bestimmten Datum?" />
	<meta property="og:image" content="/og-image.png" />
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
