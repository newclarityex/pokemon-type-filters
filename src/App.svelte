<script lang="ts">
  import { DATA, COLORS } from "./utils/pokemonTypes";

  const generations = Object.keys(DATA);

  let selectedGeneration = generations[2];

  let selectedColumns: string[] = [];

  function toggleColumn(type: string) {
    if (selectedColumns.includes(type)) {
      selectedColumns = selectedColumns.filter((t) => t !== type);
    } else {
      selectedColumns = [...selectedColumns, type];
    }
    if (selectedColumns.length > 2) {
      selectedColumns.shift();
      selectedColumns = [...selectedColumns];
    }
  }

  function calculateSelectedDamage(
    attackingType: string,
    selectedColumns: string[]
  ) {
    return selectedColumns.reduce((acc, defendingType) => {
      const value = DATA[selectedGeneration][defendingType][attackingType];
      if (value !== undefined) {
        return acc * value;
      }
      return acc;
    }, 1);
  }

  let amongus = false;

  function getImage(value: number) {
    if (value === 0) return "/Black.webp";
    if (value === 0.5) return "/Red.webp";
    if (value > 2) return "/Brown.webp";
    if (value > 1) return "/Green.webp";
  }
</script>

<main>
  {#each generations as generation}
    <button
      class={`mx-2 border border-black ${
        selectedGeneration === generation ? "bg-blue-400" : ""
      }`}
      on:click={() => (selectedGeneration = generation)}
    >
      {generation}
    </button>
  {/each}

  <button
    class={`mx-2 border border-black ${amongus ? "bg-blue-400" : ""}`}
    on:click={() => (amongus = !amongus)}>Amongus Mode</button
  >

  <h1>Click the column labels below to view resulting damage.</h1>

  <table>
    <thead>
      <tr>
        <th />
        {#each Object.keys(DATA[selectedGeneration]) as defendingType}
          <th
            class="text-center font-semibold font-mono p-0"
            style:background-color={COLORS[defendingType]}
          >
            <button class="px-2" on:click={() => toggleColumn(defendingType)}>
              {defendingType.slice(0, 3).toUpperCase()}
            </button>
          </th>
        {/each}
        <th />
        <th class="text-center font-semibold font-mono p-0">
          {#if selectedColumns.length === 0}
            <div class="px-2 bg-gray-400">SUM</div>
          {:else}
            {#each selectedColumns as type}
              <button
                class="px-2"
                on:click={() => toggleColumn(type)}
                style:background-color={COLORS[type]}
              >
                {type.slice(0, 3).toUpperCase()}
              </button>
            {/each}
          {/if}
        </th>
      </tr>
    </thead>
    <tbody>
      {#each Object.keys(DATA[selectedGeneration]) as attackingType}
        <tr>
          <td
            class="text-center font-semibold font-mono px-2"
            style:background-color={COLORS[attackingType]}
          >
            {attackingType.slice(0, 3).toUpperCase()}
          </td>
          {#each Object.keys(DATA[selectedGeneration]) as defendingType}
            <td
              class="text-center border-x border-gray-400"
              style={selectedColumns.includes(defendingType)
                ? `
                background-color: ${COLORS[attackingType] + "80"};
              `
                : `
                background-color: ${COLORS[attackingType] + "40"};
                `}
            >
              {#if amongus}
                {#if DATA[selectedGeneration][defendingType][attackingType] !== 1}
                  <img
                    class="w-6"
                    src={getImage(
                      DATA[selectedGeneration][defendingType][attackingType]
                    )}
                  />
                {/if}
              {:else}
                {DATA[selectedGeneration][defendingType][attackingType] ?? ""}
              {/if}
            </td>
          {/each}
          <td
            class="text-center font-semibold font-mono px-2"
            style:background-color={COLORS[attackingType]}
          >
            {attackingType.slice(0, 3).toUpperCase()}
          </td>
          <td
            class="text-center"
            style={`background-color: ${COLORS[attackingType] + "40"}`}
          >
            {#if amongus}
              {#if calculateSelectedDamage(attackingType, selectedColumns) !== 1}
                <img
                  class="w-6"
                  src={getImage(
                    calculateSelectedDamage(attackingType, selectedColumns)
                  )}
                />
              {/if}
            {:else}
              {calculateSelectedDamage(attackingType, selectedColumns) === 1
                ? ""
                : calculateSelectedDamage(attackingType, selectedColumns)}
            {/if}
          </td>
        </tr>
      {/each}
    </tbody>
  </table>
</main>
