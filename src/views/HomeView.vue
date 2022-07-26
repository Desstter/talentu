<template>
<b-container>
    <div align="center" class="mt-5">
        <b-img fluid width="350" src="https://www.talentu.co/wp-content/uploads/2022/03/talentuColorBlanco-2.png" />
    </div>
    <b-container id="res" class="mt-3">
        <b-row>
            <b-col>
                <b-form @submit="onSubmit">
                    <b-form-group id="input-group-1" label="Correo:" label-for="input-1">
                        <b-form-input id="input-1" v-model="newReg.email" @change="null" type="email" placeholder="Ingresa tu correo" required></b-form-input>
                    </b-form-group>

                    <b-form-group id="input-group-2" label="Nombre:" label-for="input-2">
                        <b-form-input id="input-2" v-model="newReg.first_name" placeholder="Ingresa tus nombres" required></b-form-input>
                    </b-form-group>

                    <b-form-group id="input-group-3" label="Apellido:" label-for="input-3">
                        <b-form-input id="input-3" v-model="last_name" placeholder="Ingresa tus apellidos" required></b-form-input>
                    </b-form-group>
                    <b-form-group id="input-group-4" label="Fecha de Nacimiento:" label-for="input-4">
                        <date-picker id="input-4" :disabled-date="(date) => date >= new Date()" :editable="false" v-model="birthDate" valueType="format"></date-picker>
                    </b-form-group>
                    <div align="center">
                        <b-button class="my-4" type="submit" variant="primary">Agregar Usuario</b-button>
                    </div>
                </b-form>
            </b-col>
            <b-col>
                <b-table striped hover :items="items" :fields="fields"></b-table>
            </b-col>
        </b-row>
    </b-container>
</b-container>
</template>

<script>
import axios from "axios"
import DatePicker from 'vue2-datepicker';
import 'vue2-datepicker/index.css';
export default {
    name: 'HomeView',
    components: {
        DatePicker
    },
    data() {
        return {
            fields: [{
                    key: 'id',
                    label: 'ID'
                },
                {
                    key: 'email',
                },
                {
                    key: 'first_name',
                    label: 'Nombre'
                },
                {
                    key: 'age',
                    label: 'Edad',
                }
            ],
            items: [],
            newReg: {},
            lastId: 0,
            birthDate: '',
            last_name: '',
            age: Number
        }
    },
    mounted() {
        this.sendRequest()
    },
    methods: {
        sendRequest() {
            if (localStorage.getItem('data')) {
                const data = JSON.parse(localStorage.getItem('data'))
                data.forEach(item => {
                    this.items.push(item)
                })
                this.lastId = data[data.length - 1].id
            } else {
                axios.get(`https://reqres.in/api/users?page=1`)
                    .then((res) => {
                        if (typeof (Storage) !== "undefined") {
                            localStorage.setItem('data', JSON.stringify(res.data.data))
                        }
                        res.data.data.forEach(item => {
                            this.items.push(item)
                        })
                        this.lastId = res.data.data[res.data.data.length - 1].id
                    })
                    .catch((err) => {
                        console.error(err);
                    })
            }
        },
        onSubmit(e) {
            e.preventDefault();
            if (this.birthDate == '') {
                alert('Ingresa una fecha de nacimiento valida')
            } else {
                this.newReg.id = this.lastId + 1
                this.lastId = this.newReg.id
                let birth = new Date(this.birthDate)
                let today = new Date()
                this.age = today.getFullYear() - birth.getFullYear()
                let month = today.getMonth() - birth.getMonth()
                if (month < 0 || (month === 0 && today.getDate() < birth.getDate() + 1)) {
                    this.age--;
                }
                this.newReg.age = this.age
                this.items.push(this.newReg)
                this.newReg = {}
                this.last_name = ''
                this.birthDate = ''
            }
        }
    },
}
</script>

<style>
body {
    background-image: url(https://www.talentu.co/wp-content/uploads/2020/10/bck-patron-min.png);
    background-repeat: no-repeat;
    background-size: cover;
}

#res {
    border: groove 5px black;
    background-color: rgba(255, 255, 255, 0.8);
    overflow-y: scroll;
    max-height: 400px;
}
</style>
