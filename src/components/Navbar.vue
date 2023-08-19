<template>
    <nav id="nav">
        <router-link to="/" id="logo-url">
            <img :src="logo" :alt="alt" id="logo"/>
        </router-link> 
        <router-link id="home-link" to="/">Home</router-link> 
        <router-link id="order-link" to="/pedidos">Pedidos</router-link>
    </nav>
 
</template>

<script>

    export default {
        name: "Navbar",
        props: ["logo", "alt"]
    }

</script>

<style scoped>
#nav{
    background-color: #222;
    border-bottom: 4px solid #111;
    padding: 15px 50px;
    display: flex;
    justify-content: flex-end;
    align-items: center;
}

#nav a {
    color: #fcba03;
    text-decoration: none;
    margin: 12px;
    transition: .8s;
}
#nav a:hover {
    color: #fff;
}

#nav #logo-url {
    margin: auto;
    margin-left: 0;
}

#logo {
    height: 40px;
    width: 40px;
}

</style>