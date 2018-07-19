<template>
    <div>
        <h2>List of Users</h2>
        <div v-if="error" class="error">
            {{ error }}
        </div>
        <ul v-if="users">
            <li v-for="user in users" :key="user.id">
                <strong>Name:</strong> {{ user.name }},
                <strong>Email:</strong> {{ user.email }}
            </li>
        </ul>
        <div class="pagination">
            <button :disabled="! prevPage" @click.prevent="goToPrev"> Prev</button>
            {{ paginationCount }}
            <button :disabled="! nextPage" @click.prevent="goToNext"> Next</button>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

const getUsers = (page, callback) => {
    const params = { page };

    axios.get('/api/users', { params })
        .then(response => {
            callback(null, response.data);
        })
        .catch(error => {
            callback(error, error.response.data);
        });
}

export default {
    data() {
        return {
            users: null,
            meta: null,
            links: {
                first: null,
                last: null,
                next: null,
                prev: null
            },
            error: null
        };
    },
    methods: {
        goToNext() {
            this.$router.push({
                query: {
                    page: this.nextPage,
                }
            })
        },
        goToPrev() {
            this.$router.push({
                name: 'users.index',
                query: {
                    page: this.prevPage
                }
            });
        },
        setData(err, { data: users, links, meta }) {
            if (err) {
                this.error = err.toString();
            } else {
                this.users = users;
                this.links = links;
                this.meta = meta;
            }
        }
    },
    computed: {
        nextPage() {
            if (! this.meta || this.meta.current_page === this.meta.last_page) {
                return;
            }

            return this.meta.current_page++;
        },
        prevPage() {
            if (! this.meta || this.meta.current_page === 1) {
                return;
            }

            return this.meta.current_page--;
        },
        paginationCount() {
            if (! this.meta) {
                return;
            }

            const { current_page, last_page } = this.meta;

            return `${current_page} of ${last_page}`;
        },
    },
    beforeRouteEnter (to, from, next) {
        getUsers(to.query.page, (err, data) => {
            next(vm => vm.setData(err, data));
        });
    },
    beforeRouteUpdate (to, from , next) {
        this.users = this.links = this.meta = null;

        getUsers(to.query.page, (err, data) => {
            this.setData(err, data);
            next();
        });
    }
}
</script>
