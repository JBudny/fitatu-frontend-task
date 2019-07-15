<template>
    <div class="page employees-list">
        <h1 class="employees-list__header">Employees</h1>
        <div v-if="loading" class="employees-list__loading">Loading...</div>
        <table v-else class="employees-list__list">
            <tr class="employees-list__list-header">
                <th>id</th>
                <th>Name</th>
                <th>Address</th>
                <th>Phone</th>
                <th>Email</th>
                <th>Options</th>
            </tr>
            <tr v-for="employee in employees" class="employees-list__list-row">
                <td>{{employee.id}}</td>
                <td>{{employee.name}}</td>
                <td>{{employee.address.street}} {{employee.address.suite}} {{employee.address.city}}</td>
                <td>{{employee.phone}}</td>
                <td><a :href="`mailto:${ employee.email }`">{{employee.email}}</a></td>
                <td style="vertical-align: inherit;">
                    <EditButton 
                    :edit="employee.edit" 
                    @editEmployee="editEmployee(employee)"
                    @saveChanges="saveChanges(employee)"
                    @cancelChanges="cancelChanges(employee)"
                    />
                </td>
            </tr>
        </table>
    </div>
</template>
<script>
    import axios from 'axios';
    import EditButton from '@components/EditButton'

    export default {
        components: {
            EditButton
        },
        data() {
            return {
                loading: false,
                employees: [],
            }
        },
        created () {
            this.fetchData()
        },
        watch: {
            '$route': 'fetchData'
        },
        methods: {
            fetchData () {
                this.loading = true;

                axios.get('https://jsonplaceholder.typicode.com/users')
                    .then(({data}) => {
                        data.forEach((employee)=>employee.edit=false);
                        this.employees = data;
                    })
                    .finally(() => {
                        this.loading = false;
                    })
            },
            editEmployee (employee) {
                employee.edit=true;
            },
            cancelChanges (employee) {
                employee.edit = false;
            },
            saveChanges (employee) {
                employee.edit = false;
          }
        }
    };

</script>
<style lang="scss" scoped>
    .employees-list {
        &__header {
            font-size: 20px;
            padding: 0 0 10px;
        }

        &__loading {
            color: #999d9d;
            text-align: center;
        }

        &__list {
            font-size: 14px;
            width: 100%;
            border-collapse: collapse;
        }

        &__list-header {
            background: #f7f8f9;
            border-bottom: 1px solid #999d9d;

            th {
                padding: 8px;
            }
        }

        &__list-row {
            background: #fff;

            td {
                padding: 8px;
            }
        }
    }
</style>