<script lang="ts">
    import {crates, indexes} from './website_db.json'
    import CrateList from "./lib/CrateList.svelte";
    import type {Crate, Indexes} from "./crate-db";
    import Filter from "./lib/Filter.svelte";
    import ColumnSelector from "./lib/ColumnSelector.svelte";

    const t_crates = crates as Crate[];
    let t_indexes = indexes as Indexes;

    let selected_d: number[][] = [];
    let selected_l: number[][] = [];
    let selected_r: number[][] = [];
    let selected_i: number[][] = [];

    let cols: string[] = ["name", "description", "downloads", "updated_at"];

    function combine_filters(crate_length: number, selected: any): number[] {
        let selected_crates = Array.from({length: crate_length}, (_, i) => i + 1);

        for (const filter of selected) {
            if (filter === undefined || filter.length === 0) {
                continue;
            }

            let selected_filter = filter.flat();
            selected_crates = selected_crates.filter(crate => selected_filter.includes(crate));
        }

        return selected_crates;
    }

    $: selected_crates = combine_filters(t_crates.length, [selected_d, selected_l, selected_r, selected_i]);
</script>

<a href="https://github.com/tdittr/drivers"><img decoding="async" width="149" height="149"
                                                 src="https://github.blog/wp-content/uploads/2008/12/forkme_right_green_007200.png?resize=149%2C149"
                                                 class="attachment-full size-full" alt="Fork me on GitHub"
                                                 loading="lazy"
                                                 data-recalc-dims="1"
                                                 style="position: absolute; top: 0; right: 0;"></a>
<h1>{selected_crates.length} awesome drivers waiting for you!</h1>
<main>
    <div class="filters">
        <Filter name="Dependencies" values={t_indexes.dependencies} bind:selected={selected_d}/>
        <Filter name="Interfaces" values={t_indexes.interfaces} bind:selected={selected_i}/>
        <Filter name="👮 License" values={t_indexes.license} bind:selected={selected_l}/>
        <Filter name="Rust Version" values={t_indexes.rust_version} bind:selected={selected_r}/>
        <ColumnSelector bind:selected={cols} options={Object.keys(t_crates[0])}/>
    </div>

    <CrateList crates={t_crates} filter={selected_crates} cols_shown={cols}/>
</main>
