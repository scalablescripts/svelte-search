<script>
    import {onMount} from 'svelte';

    let allProducts, filteredProducts = [];
    let filters = {
        s: '',
        sort: '',
        page: 1
    };
    let lastPage = 0;
    const perPage = 9;

    onMount(async () => {
        const response = await fetch('http://localhost:8000/api/products/frontend');
        const content = await response.json();
        allProducts = content;
        filteredProducts = content.slice(0, filters.page * perPage);
        lastPage = Math.ceil(content.length / perPage);
    });

    const filtersChanged = () => {
        let products = allProducts.filter(p => p.title.toLowerCase().indexOf(filters.s.toLowerCase()) >= 0 ||
            p.description.toLowerCase().indexOf(filters.s.toLowerCase()) >= 0);

        if (filters.sort === 'asc' || filters.sort === 'desc') {
            products.sort((a, b) => {
                const diff = a.price - b.price;

                if (diff === 0) return 0;

                const sign = Math.abs(diff) / diff;

                return filters.sort === 'asc' ? sign : -sign;
            });
        }

        lastPage = Math.ceil(products.length / perPage);
        filteredProducts = products.slice(0, filters.page * perPage);
    }

    const search = s => {
        filters.s = s;
        filters.page = 1;
        filtersChanged();
    }

    const sort = sort => {
        filters.sort = sort;
        filters.page = 1;
        filtersChanged();
    }

    const loadMore = () => {
        filters.page++;
        filtersChanged();
    }
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
    {#each filteredProducts as product}
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
