<script lang="ts">
	import { SpecialLink, SpecialProps } from '$lib/types.js';
	import Sortable from 'sortablejs';
	import { onMount } from 'svelte';
	import wuser from '$lib/user';

	let specialsElement: HTMLUListElement;
	export let specials: { type: SpecialLink; username: string; _id: string }[];
	let saveFailureMessage: string;

	onMount(() => {
		Sortable.create(specialsElement, {
			animation: 200,
			onUpdate: () => {
				specials = Array.from(specialsElement.querySelectorAll('li'))
					.map((li) => {
						const linkId = li.querySelector('a')?.id || '';
						return specials.find((link: { _id: string }) => link?._id === linkId);
					})
					.filter(Boolean) as { type: SpecialLink; username: string; _id: string }[];
				updateSpecials();
			}
		});
	});

	function deleteLink(event: Event) {
		const form = event.target as HTMLFormElement;
		const formData = new FormData(form);
		const id = formData.get('id') as string;
		const linkIndex = specials.findIndex((link) => link._id === id);
		specials.splice(linkIndex, 1);
		const li = specialsElement.querySelector(`li:nth-child(${linkIndex + 1})`);
		li?.remove();
		updateSpecials();
	}

	function updateSpecials() {
		fetch('/admin/links/update', {
			method: 'POST',
			body: JSON.stringify({ ref: 'specials', links: specials })
		})
			.then((res) => res.json())
			.then((res) => {
				saveFailureMessage = res.message;
			});
		$wuser.specials = specials;
	}
</script>

{#if saveFailureMessage}
	<p class="save-failure">{saveFailureMessage}</p>
{/if}
<ul bind:this={specialsElement}>
	{#if specials?.length > 0}
		{#each specials as link (link._id)}
			<li class="linkItem">
				<a
					href={SpecialProps[link.type].template(link.username)}
					target="_blank"
					rel="noopener noreferrer"
					id={link._id}
				>
					<i class="fa-brands {SpecialProps[link.type].icon}" />
					<span class="username">{link.username}</span>
				</a>
				<form on:submit|preventDefault={deleteLink} class="deleteBtn">
					<input type="hidden" name="id" value={link._id} />
					<button type="submit" class="bt-bad"><i class="fa-solid fa-trash" /></button>
				</form>
			</li>
		{/each}
	{:else}
		<p>No social links yet.</p>
	{/if}
</ul>

<style>
	ul {
		list-style: none;
		padding: 0;
		margin: 0;
	}

	li.linkItem {
		display: flex;
		align-items: center;
		padding: 0 0.5rem;
		background-color: #fafafa;
		border: 2px solid #e8e8ed;
		border-radius: 0.5rem;
		margin: 0.5rem 0;
		min-height: 3.5rem;
	}

	li.linkItem a {
		text-decoration: none;
		color: #333;
		margin-right: 1rem;
		display: flex;
		align-items: center;
	}

	li a i {
		font-size: 2rem;
		margin-right: 12px;
	}

	li a .username {
		font-size: 1.3rem;
		margin-right: 10px;
		word-break: break-all;
	}

	li form {
		margin-left: auto;
	}
</style>
