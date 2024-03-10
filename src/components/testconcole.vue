<template>
    <div>
        <button style="margin-top: 10px;" v-if="testConsole" @click="toggleShowTestConsole">
            <font-awesome-icon icon="fa-solid fa-text-height"></font-awesome-icon>
            Test Console
        </button>
        <div v-if="testConsole && showTestConsole" class="test-console">
            <button @click="deleteAllCaches" class="test-elem"> <span class="fas fa-trash"></span>
                Delete All Caches
            </button>
            <button @click="reloadPage" class="test-elem"> <span class="fas fa-sync"></span>
                Reload Page
            </button>
            <strong class="test-elem">HTTPS Test: </strong>
            <a v-bind:href="serverURL" target="_blank">link</a>
            <button @click="unregisterAllServiceWorkers" class="test-elem"> <font-awesome-icon
                    icon="fab fa-uniregistry"></font-awesome-icon>
                Unregister All ServiceWorkers
            </button>
        </div>
    </div>
</template>

<script>
export default {
    name: 'test_console',
    data() {
        return {
            serverURL: "https://school-classes-env.eba-xbp2nmqm.eu-west-2.elasticbeanstalk.com/lessons",
            testConsole: true,
            showTestConsole: false,
        }
    },
    props: [
        "lessons",
    ],
    methods: {
        reloadPage() {
            window.location.reload();
        },

        unregisterAllServiceWorkers() {
            navigator.serviceWorker.getRegistrations().then(function (registrations) {
                for (let registration of registrations) { registration.unregister() }
            });
            console.log("ServiceWorkers Unregistered");
        },

        deleteAllCaches() {
            caches.keys().then(function (names) { for (let name of names) caches.delete(name); });
            console.log("All Caches Deleted");
        },
        toggleShowTestConsole() {
            this.showTestConsole = !this.showTestConsole;
        },
    }
}
</script>
