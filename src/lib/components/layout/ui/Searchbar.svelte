<script lang="ts">
	import { SearchbarProduct } from '$lib/components/layout/ui';
	import {
		Command,
		CommandEmpty,
		CommandGroup,
		CommandInput,
		CommandItem,
		CommandList,
	} from '$lib/components/ui/command';
	import type { Product } from '$lib/types';
	import { onMount } from 'svelte';

	let inputValue = $state('');
	let listWidth = $state(0);
	let hasFocus = $state(false);

	type Props = {
		rootRef?: HTMLDivElement | null;
		inputRef?: HTMLInputElement | null;
		products: Product[];
	};

	let {
		rootRef = $bindable(null),
		inputRef = $bindable(null),
		products,
	}: Props = $props();

	const productsToShow = products.filter((product) =>
		product.name.toLowerCase().includes(inputValue.toLowerCase()),
	);

	function getCommandInputWidth() {
		if (!rootRef) return;

		const refStyles = window.getComputedStyle(rootRef);
		const refWidth = parseFloat(refStyles.width);

		listWidth = refWidth;
	}

	type MouseEventProps = MouseEvent & {
		currentTarget: EventTarget & HTMLDivElement;
	};

	function handleCommandBlur(event: MouseEventProps) {
		if (!rootRef || !inputRef) return;
		if (!rootRef.contains(event.relatedTarget as Node)) {
			hasFocus = false;
			inputValue = '';
			inputRef.blur();
		}
	}

	onMount(() => {
		getCommandInputWidth();
		window.addEventListener('resize', getCommandInputWidth);

		return () => {
			window.removeEventListener('resize', getCommandInputWidth);
		};
	});
</script>

<Command
	bind:ref={rootRef}
	class="focus-within:border-primary max-w-[450px] rounded-lg border"
	onmouseleave={(event) => handleCommandBlur(event)}
>
	<label
		for="searchbar"
		class="sr-only"
	>
		Searchbar
	</label>
	<CommandInput
		bind:ref={inputRef}
		bind:value={inputValue}
		id="searchbar"
		placeholder="Search..."
		class="h-9 text-sm"
		onfocus={() => (hasFocus = true)}
	/>
	{#if !!inputValue && hasFocus}
		<div class="fixed top-0 z-[100] -ml-8 px-8 pt-[17.5rem] pb-8 lg:pt-16">
			<CommandList
				class="bg-popover rounded-b-lg border max-lg:max-h-[550px]"
				style={`width: ${listWidth}px;`}
			>
				<CommandEmpty>No results found.</CommandEmpty>
				<CommandGroup heading="Search Results:">
					{#each productsToShow as { id, name, price, quantity, imageUrl, imageDescription }}
						<CommandItem class="my-2">
							<SearchbarProduct
								{id}
								{name}
								{price}
								{quantity}
								{imageUrl}
								{imageDescription}
							/>
						</CommandItem>
					{/each}
				</CommandGroup>
			</CommandList>
		</div>
	{/if}
</Command>
