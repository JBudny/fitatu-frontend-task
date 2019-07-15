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
                <template v-if="!employee.edit" >
                    <td data-label="id:">{{employee.id}}</td>
                    <td data-label="Name:">{{employee.name}}</td>
                    <td data-label="Address:">{{employee.address.street}} {{employee.address.suite}} {{employee.address.city}}</td>
                    <td data-label="Phone:">{{employee.phone}}</td>
                    <td data-label="Email:"><a :href="`mailto:${ employee.email }`">{{employee.email}}</a></td>
                </template>
                <template v-else>
                        <td data-label="id:">
                            <input
                                v-bind:class="{ isError: employee.idError }"
                                type="text"
                                v-model="employee.id"
                                placeholder="Id"
                                v-on:input="validateId(employee)">
                        </td>
                        <td data-label="Name:">
                            <input
                                v-bind:class="{ isError: employee.nameError }"
                                type="text"
                                v-model="employee.name"
                                placeholder="Name"
                                v-on:input="validateName(employee)">
                        </td>
                        <td data-label="Address (street, suite, city):">
                            <input
                                name="street"
                                v-bind:class="{ isError: employee.addressError }"
                                type="text"
                                v-model="employee.address.street"
                                placeholder="Street"
                                v-on:input="validateAddress(employee)">
                            <input
                                name="Suite"
                                v-bind:class="{ isError: employee.addressError }"
                                type="text"
                                v-model="employee.address.suite"
                                placeholder="Suite"
                                v-on:input="validateAddress(employee)">
                            <input
                                name="City"
                                v-bind:class="{ isError: employee.addressError }"
                                type="text"
                                v-model="employee.address.city"
                                placeholder="City"
                                v-on:input="validateAddress(employee)">
                        </td>
                        <td data-label="Phone:">
                            <input
                                v-bind:class="{ isError: employee.phoneError }"
                                type="text"
                                v-model="employee.phone"
                                placeholder="Phone number"
                                v-on:input="validatePhone(employee)">
                        </td>
                        <td data-label="Options:">
                            <input
                                v-bind:class="{ isError: employee.emailError }"
                                type="text"
                                v-model="employee.email"
                                placeholder="E-mail address"
                                v-on:input="validateEmail(employee)">
                        </td>
                </template>
                <td style="vertical-align: inherit;">
                    <EditButton 
                        :edit="employee.edit" 
                        :validated="employee.validated"
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
                originalEmployees: [],
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
                        data.forEach((employee)=>{
                            employee.idCopy=employee.id;
                            employee.edit=false;
                            employee.idError=false;
                            employee.nameError=false;
                            employee.addressError=false;
                            employee.phoneError=false;
                            employee.emailError=false;
                            employee.validated=false;
                        });
                        this.employees = data;
                    })
                    .finally(() => {
                        this.loading = false;
                    })
            },
            editEmployee (employee) {
                this.originalEmployees.push(JSON.parse(JSON.stringify(employee)));
                employee.edit=true;
            },
            cancelChanges (employee) {
                for(let i=0;i<=this.originalEmployees.length-1;i++){
                    if(this.originalEmployees[i].id===employee.idCopy) {
                        Object.assign(employee, this.originalEmployees[i]);
                        this.originalEmployees.splice(i,1);
                    }
                }
                employee.edit = false;
            },
            saveChanges (employee) {
                let { id, name, phone, email,
                    address } = employee;
                let { street, suite, city } = address;
                        axios.put(`https://jsonplaceholder.typicode.com/users/${id}`,
                        {
                            id: id,
                            name: name,
                            address: {
                                street: street,
                                suite: suite,
                                city: city
                            },
                            phone: phone,
                            email: email
                        },
                        {
                            headers: {"Content-type": "application/json; charset=UTF-8"}
                        })
                        .then(res => console.log(res.status))
                        .catch(err => console.log(err));
                        employee.edit = false;
                        employee.validated = false;
            },
            enableButton (employee) {
                let { idError, nameError, addressError,
                    phoneError, emailError, validated } = employee;
                if (idError===false &&
                    nameError===false &&
                    addressError===false &&
                    phoneError===false &&
                    emailError===false) {
                        employee.validated = true;
                    }
            },
            validateId (employee) {
                if (isNaN(employee.id) || employee.id.toString().length<1) {
                    employee.idError = true;
                    employee.validated = false;
                } else {
                    employee.idError = false;
                    this.enableButton(employee);
                };
            },
            validateName (employee) {
                if (employee.name.length<=0) {
                    employee.nameError = true;
                    employee.validated = false;
                } else {
                    employee.nameError = false;
                    this.enableButton(employee);
                };
            },
            validateAddress (employee) {
                if (employee.address.street.length <=0
                    || employee.address.suite.length <=0
                    || employee.address.city.length <=0 ) {
                    employee.addressError = true;
                    employee.validated = false;
                } else {
                    employee.addressError = false;
                    this.enableButton(employee);
                }
            },
            validatePhone (employee) {
                if (employee.phone.length <=0) {
                    employee.phoneError = true;
                    employee.validated = false;
                } else {
                    employee.phoneError = false;
                    this.enableButton(employee);
                }
            },
            validateEmail (employee) {
                const emailReg = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
                if (emailReg.test(employee.email)) {
                    employee.emailError = false;
                    this.enableButton(employee);
                } else {
                    employee.emailError = true;
                    employee.validated = false;
                }
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
                input {
                    min-width: 10px;
                    max-width: 100%;
                    width: 100%;
                    border-width: 1px !important;
                    margin: 0.125rem;
                }
                .isError {
                    background: #ffd6d6;
                }
            }
        }

        @media screen and (max-width: 768px) {
            .employees-list {
                &__header {
                    text-align: center;
                    font-size: 1.5rem;
                    padding: 0;
                }

                &__list {
                    border: 0;
                }

                &__list-header {
                    border: none;
                    clip: rect(0 0 0 0);
                    height: 1px;
                    margin: -1px;
                    overflow: hidden;
                    padding: 0;
                    position: absolute;
                    width: 1px;
                }

                &__list-row {
                    border-bottom: 3px solid #ddd;
                    display: block;
                    margin-bottom: .625em;
                    box-shadow: 0.125rem 0.125rem 0.625rem #aaaaaa;

                    td {
                        border-bottom: 1px solid #ddd;
                        display: block;
                        font-size: .81rem;
                        text-align: right;

                        input {
                            font-size: 1.2rem;
                            margin-top:0.250rem;
                        }

                        &:before {
                            content: attr(data-label);
                            float: left;
                            text-transform: uppercase;
                            margin-left: 2px;
                        }

                        &:last-child {
                            border-bottom: 0;
                        }
                    }
                }
            }
        }
    }
</style>