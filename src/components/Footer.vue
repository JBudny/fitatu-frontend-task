<template>
    <footer class="page-footer">
        <div class="page-footer__fitatu-info">© Fitatu 2019. Wszelkie prawa zastrzeżone</div>
    </footer>
</template>
<style lang="scss" scoped>
    .page-footer {
        height: 70px;
        width: 100%;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        align-items: center;
        padding: 15px 50px;
        background-color: #f7f8f9;
        z-index: 3;
        bottom: 0;

        &__fitatu-info {
            font-size: 12px;
            text-align: center;
            flex: 1;
        }
    }
</style>