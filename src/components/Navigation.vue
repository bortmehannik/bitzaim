<template>
    <div class="bz-group">
        <div class="bz-logo">
            <a href="/">
                <img src="../assets/images/bitzaim.svg" alt="Bitzaim">
            </a>
        </div>
        <el-menu
        class="bz-menu"
        mode="horizontal"
        >
            <el-sub-menu index="1">
                <template #title>Eng</template>
                <el-menu-item index="1-1">Rus</el-menu-item>
            </el-sub-menu>
            <el-sub-menu index="2">
                <template #title>Company</template>
                <el-menu-item index="2-1">Bitzaim</el-menu-item>
                <el-menu-item index="2-2">Valve</el-menu-item>
                <el-menu-item index="2-3">Meta</el-menu-item>
            </el-sub-menu>
            <el-menu-item index="3">Login/Sign up</el-menu-item>
        </el-menu>
    </div>
</template>

<style scoped>
    .bz-logo {
        width: 150px;
    }

    .bz-group {
        display: flex;
        align-items: center;
        border-bottom: 1px solid #dcdfe6;
        padding: 0 10px;
        justify-content: space-between;
    }

    .bz-menu {
        width: calc(100% - 150px);
        justify-content: flex-end;
        border-bottom: none;
    }

    @media(max-width: 500px) {
        .bz-logo {
            width: 100px;
        }
    }
</style>
