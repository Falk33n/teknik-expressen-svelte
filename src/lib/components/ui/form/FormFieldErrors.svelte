<script lang="ts">
	import { cn } from '$lib/utils';
	import type { WithoutChild } from 'bits-ui';
	import * as FormPrimitive from 'formsnap';

	type Props = WithoutChild<FormPrimitive.FieldErrorsProps> & {
		errorClasses?: string | undefined | null;
	};

	let {
		ref = $bindable(null),
		class: className,
		errorClasses,
		children: childrenProp,
		...props
	}: Props = $props();
</script>

<FormPrimitive.FieldErrors
	class={cn('text-destructive text-[0.8rem] font-medium', className)}
	{...props}
>
	{#snippet children({ errors, errorProps })}
		{#if childrenProp}
			{@render childrenProp({ errors, errorProps })}
		{:else}
			{#each errors as error}
				<div
					{...errorProps}
					class={cn(errorClasses)}
				>
					{error}
				</div>
			{/each}
		{/if}
	{/snippet}
</FormPrimitive.FieldErrors>
