<template>
    <div class="area-my-boards">
        <!-- 헤더영역 -->
        <div class="m-header type02">
            <div class="wrap">
                <div class="left">
                    <button class="btn-util" @click="$router.back()">
                        <img src="/images/back.png" alt="" style="width:10px;">
                    </button>
                </div>

                <div class="center">
                    <h3 class="title">내 행사 관리</h3>
                </div>

                <div class="right"></div>
            </div>
        </div>

        <!-- 내용 영역 -->
        <div class="container">
            <div class="mt-32"></div>

            <div class="wrap">
                <div class="m-tabs type03">
                    <nuxt-link to="/mypage/participants" class="m-tab">내가 참여한 행사</nuxt-link>
                    <nuxt-link to="/mypage/meetings" class="m-tab active">내가 만든 행사</nuxt-link>
                </div>

                <div class="mt-20"></div>

                <div class="m-input-select type03">
                    <select>
                        <option value="">등록순</option>
                    </select>
                </div>

                <div class="mt-12"></div>

<!--                <div class="m-values type01">
                    <div class="m-value">
                        <h3 class="title">총 게시글 수</h3>
                        <p class="count">{{ items.meta.totalPostCount }}</p>
                    </div>

                    <div class="m-value">
                        <h3 class="title">총 답글 수</h3>
                        <p class="count">{{ items.meta.totalCommentCount }}</p>
                    </div>

                    <div class="m-value">
                        <h3 class="title">받은 좋아요 수</h3>
                        <p class="count">{{ items.meta.totalLikeCount }}</p>
                    </div>
                </div>-->

                <div class="m-boards type02">
                    <post v-for="item in items.data" :key="item.id" :item="item" @removed="removed"/>
                </div>
            </div>

            <scroll-loading @load="loadMore" v-if="items.links.next" />
        </div>

        <!-- 하단 네비게이션바 -->
        <navigation />
    </div>
</template>

<script>
import Form from "@/utils/Form";
import Comment from "@/components/mypage/comment";
import Post from "~/components/mypage/post";

export default {
    components: {Post, Comment},
    auth: true,
    data() {
        return {
            form: {
                page: 1,
                board: "meetings"
            },

            items: {
                data: [],
                meta: {
                    totalCommentCount: 0,
                    totalLikeCount: 0,
                    totalPostCount: 0,
                },
                links: {

                }
            }

        }
    },
    methods: {
        loadMore(state) {
            if(this.items.meta.current_page <= this.items.meta.last_page){
                this.form.page += 1;

                this.$axios.get("/api/my/posts", {
                    params: this.form
                }).then(response => {
                    this.items = {
                        ...response.data,
                        data: [...this.items.data, ...response.data.data]
                    };

                    state.loaded();
                });
            }
        },

        getItems(){
            this.form.page = 1;

            this.$axios.get("/api/my/posts", {
                params: this.form
            }).then(response => {
                this.items = response.data;
            });
        },

        removed(){
            this.getItems();
            // this.items.data = this.items.data.filter(itemData => itemData.id != item.id);
        }
    },

    mounted() {
        this.getItems();
    }
}
</script>

<style scoped>

</style>
