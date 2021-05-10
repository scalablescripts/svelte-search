<script>
    import {onMount} from 'svelte';

    let products = [];
    let filters = {
        s: '',
        sort: '',
        page: 1
    };
    let lastPage = 0;

    const load = async () => {
        const arr = [];

        if (filters.s) {
            arr.push(`s=${filters.s}`);
        }

        if (filters.sort) {
            arr.push(`sort=${filters.sort}`);
        }

        if (filters.page) {
            arr.push(`page=${filters.page}`);
        }

        const response = await fetch(`http://localhost:8000/api/products/backend?${arr.join('&')}`);
        const content = await response.json();
        products = filters.page === 1 ? content.data : [...products, ...content.data];
        lastPage = content.last_page;
    }

    const search = s => {
        filters.s = s;
        filters.page = 1;
        load();
    }

    const sort = sort => {
        filters.sort = sort;
        filters.page = 1;
        load();
    }

    const loadMore = () => {
        filters.page++;
        load();
    }

    onMount(load);
</script>

<div class="col-md-12 mb-4 input-group">
    <input class="form-control" placeholder="Search" on:keyup={e => search(e.target.value)}/>
    <div class="input-group-append">
        <select class="form-select" on:change={e => sort(e.target.value)}>
            <option>Select</option>
            <option value="asc">Price Ascending</option>
            <option value="desc">Price Descending</option>
        </select>
    </div>
</div>

<div class="row row-cols-1 row-cols-sm-2 row-cols-md-3 g-3">
    {#each products as product}
        <div class="col">
            <div class="card shadow-sm">
                <img src={product.image} alt={product.image} height="200">

                <div class="card-body">
                    <p class="card-text">{product.title}</p>
                    <div class="d-flex justify-content-between align-items-center">
                        <small class="text-muted">${product.price}</small>
                    </div>
                </div>
            </div>
        </div>
    {/each}
</div>

{#if filters.page < lastPage}
    <div class="d-flex justify-content-center mt-4">
        <button class="btn btn-primary" on:click={loadMore}>Load More</button>
    </div>
{/if}
