<script lang="ts">
	import { TelInput, normalizedCountries, isSelected, clickOutsideAction } from 'svelte-tel-input';
	// import 'svelte-tel-input/styles/flags.css';

	export let clickOutside = true;
	export let closeOnClick = true;
	export let disabled = false;
	export let detailedValue = null;
	export let value = '+79272264105';
	export let searchPlaceholder = 'Поиск по стране';

	let searchText = '';
	let isOpen = false;
	export let selectedCountry = 'GB';
	export let valid = true;
	export let options = { invalidateOnCountryChange: true };

	$: selectedCountryDialCode =
		normalizedCountries.find((el) => el.iso2 === selectedCountry)?.dialCode || null;

	const toggleDropDown = (e) => {
		e?.preventDefault();
		if (disabled) return;
		isOpen = !isOpen;
	};

	const closeDropdown = (e) => {
		if (isOpen) {
			e?.preventDefault();
			isOpen = false;
			searchText = '';
		}
	};

	const selectClick = () => {
		if (closeOnClick) closeDropdown();
	};

	const closeOnClickOutside = () => {
		if (clickOutside) {
			closeDropdown();
		}
	};

	const sortCountries = (countries, text) => {
		const normalizedText = text.trim().toLowerCase();
		if (!normalizedText) {
			return countries.sort((a, b) => a.label.localeCompare(b.label));
		}
		return countries.sort((a, b) => {
			const aNameLower = a.name.toLowerCase();
			const bNameLower = b.name.toLowerCase();
			const aStartsWith = aNameLower.startsWith(normalizedText);
			const bStartsWith = bNameLower.startsWith(normalizedText);
			if (aStartsWith && !bStartsWith) return -1;
			if (!aStartsWith && bStartsWith) return 1;
			const aIndex = aNameLower.indexOf(normalizedText);
			const bIndex = bNameLower.indexOf(normalizedText);
			if (aIndex === -1 && bIndex === -1) return a.id.localeCompare(b.id);
			if (aIndex === -1) return 1;
			if (bIndex === -1) return -1;
			const aWeight = aIndex + (aStartsWith ? 0 : 1);
			const bWeight = bIndex + (bStartsWith ? 0 : 1);
			return aWeight - bWeight;
		});
	};

	const handleSelect = (val, e?) => {
		if (disabled) return;
		e?.preventDefault();
		if (
			selectedCountry === undefined ||
			selectedCountry === null ||
			(typeof selectedCountry === typeof val && selectedCountry !== val)
		) {
			selectedCountry = val;
			onChange(val);
			selectClick();
		} else {
			dispatch('same', { option: val });
			selectClick();
		}
	};
</script>

<div
	class="phone-input-wrapper {valid
		? `input-valid`
		: `input-invalid`}"
>
	<div class="country-select-wrapper" use:clickOutsideAction={closeOnClickOutside}>
		<button
			id="countries-button"
			data-dropdown-toggle="dropdown-countries"
			class="select-btn"
			type="button"
			role="combobox"
			aria-controls="dropdown-countries"
			aria-expanded="false"
			aria-haspopup="false"
			on:click={toggleDropDown}
		>
			{#if selectedCountry && selectedCountry !== null}
				<div class="country-select-inner">
					<span class="flag flag-{selectedCountry.toLowerCase()}" />
					<span>+{selectedCountryDialCode}</span>
				</div>
			{:else}
				Please select
			{/if}
			<svg
				aria-hidden="true"
				class="{isOpen ? 'rotate-180' : ''}"
				fill="currentColor"
				viewBox="0 0 20 20"
				xmlns="http://www.w3.org/2000/svg"
			>
				<path
					fill-rule="evenodd"
					d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
					clip-rule="evenodd"
				/>
			</svg>
		</button>
		{#if isOpen}
			<div
				id="dropdown-countries"
				class="select-dropdown"
				data-popper-reference-hidden=""
				data-popper-escaped=""
				data-popper-placement="bottom"
				aria-orientation="vertical"
				aria-labelledby="country-button"
				tabindex="-1"
			>
				<div
					class="dropdown-inner"
					aria-labelledby="countries-button"
					role="listbox"
				>
					<input
						aria-autocomplete="list"
						type="text"
						bind:value={searchText}
						placeholder={searchPlaceholder}
					/>
					{#each sortCountries(normalizedCountries, searchText) as country (country.id)}
						{@const isActive = isSelected(country.iso2, selectedCountry)}
						<div id="country-{country.iso2}" role="option" aria-selected={isActive}>
							<button
								value={country.iso2}
								type="button"
								class="country-select-item
                            {isActive
									? 'country-active'
									: ''}"
								on:click={(e) => {
									handleSelect(country.iso2, e);
								}}
							>
								<div class="country-select-item-inner">
									<span class="flag flag-{country.iso2.toLowerCase()}" />
									<span>{country.name}</span>
									<span>+{country.dialCode}</span>
								</div>
							</button>
						</div>
					{/each}
				</div>
			</div>
		{/if}
	</div>

	<TelInput
		id="tel-input"
		bind:country={selectedCountry}
		bind:detailedValue
		bind:value
		bind:valid
		{options}
		required={true}
	/>
</div>

<style>

    :global(.dropdown-inner) {
        max-height: 330px !important;
    }
    #dropdown-countries {
        z-index: 999;
    }

    #dropdown-countries input {
        width: 100%;
        border: 0;
        padding-left: 2em;
        height: 56px;
    }

    :global(#tel-input) {
        width: 100%;
        border: 0;
    }

    :global(#tel-input:focus) {
        outline: 0;
    }

    #countries-button {
        background: white;
        border-radius: 0;
    }
	.phone-input-wrapper {
		position: relative;
		display: flex;
		border-radius: 8px;
        height: 56px;
	}

	.country-select-wrapper {
		display: flex;
	}

	.select-btn {
		position: relative;
		overflow: hidden;
		z-index: 10;
		white-space: nowrap;
		display: inline-flex;
		align-items: center;
		padding-top: 0.625rem;
		padding-bottom: 0.625rem;
		padding-left: 1rem;
		padding-right: 1rem;
		text-align: center;
		border-top-left-radius: 0.5rem;
		border-bottom-left-radius: 0.5rem;
	}
	.select-btn svg {
			color: black;
			height: 1rem;
			width: 1rem;
	}

	.select-dropdown {
		position: absolute;
		max-width: fit-content;
		background-color: white;
		border-radius: 4px;
	}
	.dropdown-inner {
		overflow-y: auto;
		max-height: 200px;
	}

	.country-select-inner, .country-select-item-inner {
		display: inline-flex;
		align-items: center;
		text-align: left;			
	}

	.country-select-inner span, .country-select-item span {
			flex-shrink: 0;
			margin-right: 0.75rem;
	}

	.country-select-item {
		display: inline-flex;
		padding-left: 1rem;
        padding-right: 1rem;
		padding-top: 0.5rem;
        padding-bottom: 0.5rem;
		overflow: hidden;
		width: 100%;
	}

  /* class for active item in country dropdown selector */
	.country-active {
		color: red;
	}

	.input-invalid {
		border-style: solid;
		border-color: red;
	}

	.input-valid{
		color: blue;
	}

	.rotate-180 {
		transform: rotate(180deg);
	}
</style>
