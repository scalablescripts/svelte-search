<script>
    import {stores} from '@sapper/app';

    const {page} = stores();
</script>

<div class="container">
    <header class="d-flex justify-content-center py-3">
        <ul class="nav nav-pills">
            <li class="nav-item">
                <a href="/frontend" class:active={$page.path.includes('/frontend')} class="nav-link">Frontend</a>
            </li>
            <li class="nav-item">
                <a href="/backend" class:active={$page.path.includes('/backend')} class="nav-link">Backend</a>
            </li>
        </ul>
    </header>
</div>
