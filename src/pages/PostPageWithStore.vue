<template>
    <h2>Страница с постами</h2>

    <my-input v-focus :model-value="searchQuery" @update:model-value="setSearchQuery" placeholder="Поиск..." />

    <div class="app__btns">
        <my-button @click="showDialog">Создать пост</my-button>

        <my-select :model-value="selectedSort" @update:model-value="setSelectedSort" :options="sortOptions" />
    </div>

    <my-dialog v-model:show="dialogVisible">
        <post-form @create="createPost" />
    </my-dialog>

    <post-list v-if="!isPostsLoading" :posts="sortedAndSearchedPosts" @remove="removePost" />

    <div v-else>Идет загрузка...</div>

    <div v-intersection="loadMorePosts" class="observer"></div>

    <div class="page__wrapper">
        <div 
            class="page" 
            :class="{'current-page': page === pageNumber}"
            v-for="pageNumber in totalPages" 
            :key="pageNumber"
            @click="changePage(pageNumber)"
        >{{ pageNumber }}</div>
    </div>
</template>

<script>
import PostForm from "@/components/PostForm.vue";
import PostList from "@/components/PostList.vue";
import { mapState, mapGetters, mapMutations, mapActions } from "vuex";

export default {
    components: {
        PostList,
        PostForm
},
    data() {
        return {
            dialogVisible: false
        }
    },
    mounted() {
        this.fetchPosts();
    },
    computed: {
        ...mapState({
            posts: state => state.post.posts,
            isPostsLoading: state => state.post.isPostsLoading,
            selectedSort: state => state.post.selectedSort,
            searchQuery: state => state.post.searchQuery,
            page: state => state.post.page,
            limit: state => state.post.limit,
            totalPages: state => state.post.totalPages,
            sortOptions: state => state.post.sortOptions
        }),
        ...mapGetters({
            sortedPosts: 'post/sortedPosts',
            sortedAndSearchedPosts: 'post/sortedAndSearchedPosts',
        })
    },
    methods: {
        ...mapMutations({
            setPage: 'post/setPage',
            setSearchQuery: 'post/setSearchQuery',
            setSelectedSort: 'post/setSelectedSort',
        }),
        ...mapActions({
            loadMorePosts: 'post/loadMorePosts',
            fetchPosts: 'post/fetchPosts',
        }),
        createPost(post) {
            this.posts.push(post);
            this.dialogVisible = false;
        },
        removePost(post) {
            this.posts = this.posts.filter(p => p.id !== post.id);
        },
        showDialog() {
            this.dialogVisible = true;
        },
    }
}
</script>

<style scoped>
.app__btns {
    display: flex;
    justify-content: space-between;
    margin: 24px 0 32px;
}

.page__wrapper {
    display: flex;
    justify-content: center;
    margin-top: 15px;
}

.page {
    border: 1px solid black;
    padding: 10px;
    margin: 15px 5px;
    cursor: pointer;
}

.page:hover {
    opacity: 0.4;
}

.current-page {
    border: 2px solid teal;
}
</style>