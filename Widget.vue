<script>
    import {mapState} from "vuex";

    export default {
        props: {
            selectedAspect: {
                type: String,
                default: "conversion_label",
            },
            metric: {
                type: String,
                required: true,
            },
            minutes: {
                type: String,
                required: true,
            },
        },
        data() {
            return {
                loader: null,
                loading: false,
                topContent: [],
            };
        },
        computed: {
            ...mapState({
                prefs: state => state.prefs,
            }),
            dateRange() {
                return utils.getRange(this.minutes, {
                    weekStart: this.prefs.week_start,
                    date: new Date(),
                });
            },
            url() {
                const query = {
                    abstracted_queries: "some_logic"
                };

                const params = {date: new Date()};

                return route({
                    name: "listing",
                    params,
                    query,
                }).href;
            },
        },
        mounted() {
            this.loading = true;

            this.data = //outside logic that pulls data from an API
            this.loading = false
        },
        watch: {
            selectedAspect: {
                handler() {
                    this.loading = true;
                },
            },
        },
    };
</script>

<template>
    <div class="top-converting">
        <div class="top-converting-header">
            Top <span class="selected-aspect">Stuff</span>
        </div>
        <ul class="top-converting-content">
            <span v-for="i in 3" :key="`top-content-placeholder-${i}`" class="placeholder">loading</span>

            <span v-if="!topContent.length"> No content for the selected period</span>

            <li v-for="item in data">

                <div class="top-converting-item">
                    <a
                        href="item"
                    >
                        {{ item.conversion_label }}
                    </a>
                </div>

                <div class="top-converting-item" :class="{'--tag': isTagAspect}">
                    <a
                        v-if="slugify(item[selectedAspect])"
                        route="meta-info"
                        :params="{
                            aspect: selectedAspect,
                            meta: slugify(item[selectedAspect]),
                        }"
                        :query="{
                            metric: metric,
                            start: dateRange.start,
                            end: dateRange.end,
                        }"
                        class="ellipsized"
                    >
                        <span class="top-converting-aspect"> {{ decodeHTML(item[selectedAspect]) }}</span>
                    </a>
                </div>
            </li>
        </ul>
    </div>
</template>

<style lang="scss" scoped>
    .top-converting {
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        margin-left: var(--content-horiz-padding);

        &-header {
            font-size: 0.9em;
        }

        &-content {
            display: flex;
            margin: 12px 0 0;
            padding: 0;
            justify-content: space-between;
            width: 100%;

            li {
                list-style: none;
                flex-direction: row;
            }
        }

        &-item {
            margin-right: 10px;
        }

        .placeholder {
            width: 100px;
        }
    }

    .top-converting-header {
        color: var(--label);
    }

    @media (max-width: $break1) {
        .top-converting-content {
            padding-left: 0;
        }
    }
    @media (max-width: $break2) {
        .top-converting {
            margin-top: 1em;

            &-content {
                flex-direction: column;
                padding-left: 0;
                width: 100%;
                gap: 10px;

                li {
                    flex-direction: column;
                }
            }
        }
    }
</style>
