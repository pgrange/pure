<template>
    <div>
        <div class="form-group row">
            <div class="col-xs-12">
                <div class="inner-addon left-addon">
                    <i class="fa fa-search"></i>
                    <input class="form-control" name="search" placeholder="Search" v-model="searchQuery">
                </div>
            </div>
        </div>
        <ul class="list-group">
            <li class="list-group-item" v-if="passwords.length === 0">
                You don't have any passwords saved in your database.
                <router-link :to="{ name: 'home'}">Would you like to create one ?</router-link>
            </li>
            <li class="list-group-item" v-for="password in filteredPasswords">
                <delete-button class="float-xs-right mt-1 text-xs-right"
                               confirmText="Are you sure you want to delete this password profile?"
                               confirmButton="Sure delete it"
                               cancelButton="Oups no!"
                               v-on:remove="deletePassword(password)">
                </delete-button>
                <ul class="list-unstyled">
                    <li>
                        <router-link :to="{ name: 'password', params: { id: password.id }}">
                            {{password.site}}
                        </router-link>
                    </li>
                    <li>
                        {{password.login}}
                    </li>
                </ul>
            </li>
        </ul>
    </div>
</template>
<script type="text/ecmascript-6">
    import DeleteButton from '../components/DeleteButton.vue';
    import {mapGetters} from 'vuex';

    function fetchPasswords(store) {
        return store.dispatch('FETCH_PASSWORDS')
    }

    export default {
        name: 'passwords-view',
        data(){
            return {
                searchQuery: ''
            }
        },
        components: {DeleteButton},
        computed: {
            ...mapGetters(['passwords', 'email']),
            filteredPasswords(){
                return this.passwords.filter(password => {
                    var loginMatch = password.login.match(new RegExp(this.searchQuery, 'i'));
                    var siteMatch = password.site.match(new RegExp(this.searchQuery, 'i'));
                    return loginMatch || siteMatch;
                })
            }
        },
        preFetch: fetchPasswords,
        beforeMount () {
            fetchPasswords(this.$store);
        },
        methods: {
            deletePassword(password){
                return this.$store.dispatch('DELETE_PASSWORD', {id: password.id});
            }
        }
    }
</script>
