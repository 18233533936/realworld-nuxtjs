<template>
    
<div class="editor-page">
    <div class="container page">
        <div class="row">

            <div class="col-md-10 offset-md-1 col-xs-12">
                <ul class="error-messages" v-if="errors">
                    <div v-for="(value, field) in errors" :key="field" class="ng-scope">
                      <li v-for="error in value" :key="error" class="ng-binding ng-scope">
                        {{field}} {{error}}
                      </li>
                    </div>
                  </ul>
                <form @submit.prevent="onSubmit">
                   
                        <fieldset class="form-group">
                            <input type="text" class="form-control form-control-lg" v-model="article.title" placeholder="Article Title">
                        </fieldset>
                        <fieldset class="form-group">
                            <input type="text" class="form-control" v-model="article.description" placeholder="What's this article about?">
                        </fieldset>
                        <fieldset class="form-group">
                            <textarea class="form-control" rows="8" v-model="article.body"
                                      placeholder="Write your article (in markdown)"></textarea>
                        </fieldset>
                        <fieldset class="form-group">
                            <input type="text" class="form-control" v-model="article.tagList" placeholder="Enter tags">
                            <div class="tag-list"></div>
                        </fieldset>
                        <button class="btn btn-lg pull-xs-right btn-primary">
                            Publish Article
                        </button>
                   
                </form>
            </div>

        </div>
    </div>
</div>


</template>

<script>
    import {
        createArticle
    } from '@/api/editor'
    import {
        updateArticle, getArticle
    } from '@/api/article'
    export default {
        // 在路由匹配组件渲染之前会先执行中间件处理
        middleware: 'authenticated',
        name: 'EditorIndex',
        props: [''],
        data() {
            return {
                errors: null,
                tagstr: '',
                article: {
                    title: "",
                    description: "",
                    body: "",
                    tagList: []
                }
            };
        },

        components: {},

        computed: {},

        beforeMount() {},

       async mounted () {
            const slug = this.$route.params.slug
            if (slug) {
            this.slug = slug
            const { data } = await getArticle(slug)
            this.article = data.article
            }
        },

        methods: {
            enterTag () {
              this.article.tagList.push(this.tagstr)
              this.tagstr = ''
            },
            removeTag (index) {
              this.article.tagList.splice(index, 1)
            },
            async onSubmit() {
                try {
                    if (this.slug) {
                    const { data } = await updateArticle(this.slug, {article: this.article})
                    this.$router.push(`/article/${data.article.slug}`)
                    }else {
                    const { data } = await createArticle({
                        article: this.article
                    })
                    this.$router.push(`/article/${data.article.slug}`)
                    }
                } catch (errors) {
                    this.errors = errors.response.data.errors
                }
            }
        },

        watch: {}

    }
</script>
<style lang='' scoped>

</style>