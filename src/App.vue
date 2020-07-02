<template>
    <div id="app">
        <header class="grid">
            <div class="title-container">
                <h1>Coding Test</h1>
            </div>
        </header>
        <main class="content grid" id="home-content">
            <div class="content-title-container">
                <h2>Accounts</h2>
            </div>
            <transition>
                <section v-if="!isLoaded" key="loading">
                    <span>Loading...</span>
                </section>
                <section v-if="isLoadingError" key="loadingError">
                    <span>
                        <strong>Error!</strong> A problem has been occurred while loading data.
                    </span>
                </section>
                <section v-if="isLoaded && !isLoadingError" class="grid" id="account-grid" key="loaded">
                    <section class="account-column grid" id="active-account-column">
                        <div class="account-container-title" id="active-account-container-title">
                            <h3>Active</h3>
                        </div>
                        <user-accounts class="active-account" :accounts="activeAccounts"></user-accounts>
                    </section>
                    <section class="account-column grid" id="overdue-account-column">
                        <div class="account-container-title" id="overdue-account-container-title">
                            <h3>Overdue</h3>
                        </div>
                        <user-accounts class="overdue-account" :accounts="overdueAccounts"></user-accounts>
                    </section>
                    <section class="account-column grid" id="inactive-account-column">
                        <div class="account-container-title" id="inactive-account-container-title">
                            <h3>Inactive</h3>
                        </div>
                        <user-accounts class="inactive-account" :accounts="inactiveAccounts"></user-accounts>
                    </section>
                </section>
            </transition>
        </main>
        <footer class="grid">
            <p class="copy">&copy; {{getCurrentYear()}}</p>
        </footer>
    </div>
</template>

<script>
    import UserAccounts from './components/user-accounts.vue';
    import ENUM from "./utils/enums";
    import axios from "axios";

    export default {
        name: 'app',
        components: {
            "user-accounts": UserAccounts
        },
        data() {
            return {
                userAccounts: [],
                isLoaded: false,
                isLoadingError: false,
            }
        },
        created() {
            this.isLoaded = false;
            this.fetchUserAccounts()
                .then((accounts) => {
                    this.userAccounts = accounts;
                    this.isLoaded = true;
                })
                .catch((error) => {
                    this.setError({ error });
                });
        },
        computed: {
            activeAccounts() {
                return this.userAccounts.filter(acct => acct.AccountStatusId == ENUM.AccountStatuses.Active);
            },
            overdueAccounts() {
                return this.userAccounts.filter(acct => acct.AccountStatusId == ENUM.AccountStatuses.Overdue);
            },
            inactiveAccounts() {
                return this.userAccounts.filter(acct => acct.AccountStatusId == ENUM.AccountStatuses.Inactive);
            }
        },
        methods: {
            async fetchUserAccounts() {
                return new Promise((resolve, reject) => {
                    axios.get('https://frontiercodingtests.azurewebsites.net/api/accounts/getall')
                        .then((response) => resolve(response.data))
                        .catch((error) => {
                            reject(error);
                        });
                });
            }, // TODO move to actions. Need to implement a store and api service pattern
            setError({ error }) {
                this.isLoadingError = true;                
            }, // TODO handle error
            getCurrentYear() {
                return new Date().getFullYear();
            }
        }
    };
</script>

<style>
    html, body {
        height: 100%;
        width: 100%;
        font-family: Roboto, Helvetica, Arial, sans-serif
    }

    article {
        height: 100%;
        grid-template-columns: 1fr;
        grid-template-areas: "header" "main" "footer";
        grid-template-rows: 100px 1fr 150px;
    }

    header {
        grid-area: header;
        background-color: #006643;
        color: #fff;
        grid-template-columns: 1% 98% 1%;
    }

    h1 {
        font-size: 3em;
        font-weight: bold;
    }

    h2 {
        font-size: 2em;
        color: #006643;
    }

    h3 {
        display: block;
        font-size: 1.5em;
        color: #006643;
    }

    main {
        grid-area: main;
    }

    footer {
        grid-area: footer;
        background-color: #006643;
        color: #fff;
        grid-template-columns: 1% 98% 1%;
        grid-template-rows: 5% 90% 5%;
        position: fixed;
        bottom: 0;
        width: 100%;
    }

    .grid {
        display: grid;
    }

    .title-container {
        grid-column-start: 2;
        align-self: center;
    }

    #home-content {
        grid-row-gap: 25px;
        grid-template-rows: 10px 100px auto;
    }

    #account-grid {
        grid-template-columns: 1fr 1fr 1fr;
        grid-row-start: 3;
    }

    .account-column {
        grid-template-rows: 2em repeat(2, 10em);
    }

    .content-title-container {
        justify-self: center;
        height: 1em;
        grid-row-start: 2;
    }

    #overdue-account-container-title > h3 {
        color: #B22222;
    }

    #inactive-account-container-title > h3 {
        color: #808080;
    }

    .account-container {
        justify-self: center;
        height: 5em;
        margin-top: 1rem;
    }

    .account-container-title {
        justify-self: center;
    }

    .account-data-list {
        list-style: none;
    }

        .account-data-list > li {
            margin: 10px 0;
        }

            .account-data-list > li > label {
                font-weight: bold;
                margin: 0 5px 0 0;
            }

    .copy {
        place-self: end;
        grid-row-start: 2;
        grid-column-start: 2;
    }
</style>
