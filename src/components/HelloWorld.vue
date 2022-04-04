<template>
<div id="app">
    <div>
        <form action="">
            <input placeholder="title" v-model="post.title">
            <input placeholder="content" v-model="post.content">
            <button @click="addNote()" type="submit">AddNote</button>
        </form>
    </div>
    <div v-for="(item, index) in listData.flat()" :key="index">
        <div>
            ID {{ item.id}}
            {{ item.title }}
            {{ item.content }}
            <span> <button @click="remove(item.id)">Delete</button></span>
        </div>
    </div>
    <div class="search">
        <input type="number" v-model="idSearch">
        <button @click="findNote(idSearch)">Search</button>
        <div class="title-by-id">Tiêu đề: {{titleFindById}}</div>
        <div class="content-by-id">Nội dung: {{contentFindById}}</div>
    </div>
</div>
</template>

<script>
export default {
    name: 'HelloWorld',
    data() {
        return {
            listData: [],
            post: {
                title: '',
                content: ''
            },
            titleFindById: '',
            contentFindById: '',
            idSearch: 100,
        }
    },
    methods: {
        getData() {
            fetch('http://192.168.1.38/note_cy/tien/listNote.php').then((res) => res.json()).then((data) => {
                this.listData = data;
            })
        },

        async addNote() {
            const formData = new FormData();
            for (const key in this.post) {
                formData.append(key, this.post[key]);
            }
            const request = new Request(
                "http://192.168.1.38/note_cy/tien/addNote.php", {
                    method: "POST",
                    mode: "cors",
                    cache: "default",
                    body: formData,
                }
            );
            const res = await fetch(request);
            const {
                photos
            } = await res.json();
            this.data = photos[0];
        },
        async remove(id) {
            const formData = new FormData();
            formData.append('id', id);
            const request = new Request(
                "http://192.168.1.38/note_cy/tien/deleteNote.php", {
                    method: "POST",
                    mode: "cors",
                    cache: "default",
                    body: formData,
                }
            );
            await fetch(request);
            this.getData();
        },
        async findNote(id) {
            fetch(`http://192.168.1.38/note_cy/tien/findById.php?id=${id}`, {
                    method: 'GET',
                })
                .then(response => response.json())
                .then((data) => {
                    this.titleFindById = data.title;
                    this.contentFindById = data.content;
                })
                .catch(error => console.log(error))
        }
    },
    mounted() {
        this.getData();
    },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->

<style scoped>
h1,
h2 {
    font-weight: normal;
}

ul {
    list-style-type: none;
    padding: 0;
}

li {
    display: inline-block;
    margin: 0 10px;
}

a {
    color: #42b983;
}
</style>
