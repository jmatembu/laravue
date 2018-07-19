<template>
    <div>
        <h1>Vue Router Demo App</h1>

        <nav>
            <router-link :to="{ name: 'home' }">Home</router-link>
            <router-link :to="{ name: 'hello' }">Hello World</router-link>
            <router-link :to="{ name: 'users.index' }">Users</router-link>
        </nav>

        <div class="container">
            <router-view></router-view>
        </div>
    </div>
</template>

<script>
export default {}
</script>
