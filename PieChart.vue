<script>
    import {pie, sum, arc} from "d3";

    export default {
        props: {
            dummyData: {
                type: Object,
                required: true,
            },
            total: {
                type: Number,
                required: true,
            },
        },
        data() {
            return {
                width: 300,
                height: 300,
                radius: 150,
                colors: ["#D7263D", "#D4ADCF", "#88CCF1", "#EFD167", "#7166B8"],
                impactChartColors: ["#324A78", "#ECECEC"],
                donut: pie().sort(null),
                arcRadius: arc().innerRadius(105).outerRadius(150),
            };
        },
        computed: {
            donutFiveState: state => state.donut(state.dummyData.map(stat => stat.revenue)),
            donutTwoState: state => state.donut([state.impact, state.impactDelta]),
            impact: state => sum(state.dummyData.map(stat => stat.revenue)),
            impactDelta: state => state.totalRevenue - state.impact,
            impactInfo: state => [
                {id: "Impacted by Content", val: state.impact},
                {id: "Unaffected by Content", val: state.impactDelta},
            ],
        },
        mounted() {},
    };
</script>
<template>
    <div class="section chart">
        <svg class="pie" id="impacted-by-content">
            <g
                v-for="(stat, idx) in donutTwoState"
                :key="idx"
                :transform="`translate(${width / 2}, ${height / 2})`"
            >
                <path :d="arcRadius(stat)" :fill="impactChartColors[idx]" />
            </g>
            <g class="percentage">
                <text text-anchor="middle" :x="width / 2 - 25" :y="height / 2">Content Contribution:</text>
                <text text-anchor="middle" font-weight="bold" :x="width / 2 + 75" :y="height / 2">
                    {{ toPercentStr(impact / total) }}
                </text>
            </g>
            <g class="total">
                <text text-anchor="middle" :x="width / 2 - 40" :y="height / 2 + 25">Total:</text>
                <text text-anchor="middle" font-weight="bold" :x="width / 2 + 45" :y="height / 2 + 25">
                    ${{ humanNumber(total) }}
                </text>
            </g>
            <g v-for="(rec, idx) in impactInfo" :key="idx" class="legend" :transform="`translate(${width / 10},10)`">
                <rect
                    :fill="impactChartColors[idx]"
                    :x="0"
                    :y="height + 36 * idx"
                    :width="'20px'"
                    :height="'20px'"
                    :transform="`translate(0, 5)`"
                    rx="3"
                />
                <text text-anchor="right" :x="30" :y="height + 35 * idx" :dy="20 + idx">
                    {{ rec.id }}: ${{ humanNumber(rec.val) }}
                </text>
            </g>
        </svg>
        <svg class="pie" id="content-breakdown">
            <g v-for="(rec, idx) in donutFiveState" :key="idx" :transform="`translate(${width / 2}, ${height / 2})`">
                <path :d="arcRadius(rec)" :fill="colors[idx]" />
            </g>
            <g class="total">
                <text text-anchor="middle" :x="width / 2 - 30" :y="height / 2">Impact:</text>
                <text text-anchor="middle" font-weight="bold" :x="width / 2 + 65" :y="height / 2">
                    ${{ humanNumber(impact) }}
                </text>
            </g>
            <g v-for="(stat, idx) in stats" :key="idx" class="legend" :transform="`translate(${width / 10},10)`">
                <rect
                    :fill="colors[idx]"
                    :x="0"
                    :y="height + 36 * idx"
                    :width="'20px'"
                    :height="'20px'"
                    :transform="`translate(0, 5)`"
                    rx="3"
                />
                <text text-anchor="right" :x="30" :y="height + 35 * idx" :dy="20 + idx">
                    {{ stat.id }}: ${{ humanNumber(stat.revenue) }}
                </text>
            </g>
        </svg>
    </div>
</template>
<style lang="scss" scoped>
    .pie {
        width: auto;
        height: 500px;
    }

    .chart {
        display: flex;
        justify-content: space-between;
        max-width: 800px;
    }

    @media (max-width: $break2) {
        .pie {
            width: 100%;
        }

        .chart {
            flex-direction: column;
            gap: 10px;
        }
    }

    @media print {
        .chart {
            break-inside: avoid;
        }
    }
</style>
