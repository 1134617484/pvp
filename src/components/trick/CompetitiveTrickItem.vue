<template>
    <div class="m-trick-item" v-if="hasData">
        <div class="m-trick-item__title">{{ data.post_title }}</div>
        <div class="m-trick-item__content">
            <div class="m-trick-item__left">
                <a class="m-author" :href="authorLink(data?.post_author)">
                    <img class="u-avatar" :src="showAvatar(data.author_info?.user_avatar)" alt="" />
                    <span class="u-name">{{ data.author_info?.display_name }}</span>
                </a>
            </div>
            <div class="m-trick-item__right">
                <div class="m-header">
                    <div v-html="nl2br(data?.post_meta?.content)"></div>
                </div>
                <div class="m-content">
                    <div class="m-talent">
                        <div class="m-talent__title">奇穴</div>
                        <div class="m-talent-box" :class="`m-qx-container-${data?.ID}`"></div>
                    </div>
                    <div class="m-skills">
                        <div class="m-skill-item" v-for="(item, i) in skills" :key="i">
                            <div class="u-title">
                                <el-icon class="u-icon"><List /></el-icon>{{ item.name }}
                            </div>
                            <div class="u-skills" v-if="item.sq">
                                <span
                                    v-for="(skill, index) in item.sq"
                                    :key="skill.SkillID + '' + index"
                                    class="u-skill"
                                >
                                    <img
                                        class="u-skill-icon"
                                        :src="iconLink(skill.IconID)"
                                        :alt="skill.IconID"
                                        :title="skill.Name"
                                    />
                                    <span class="u-skill-name">{{ skill.Name }}</span>
                                </span>
                            </div>
                            <div class="u-desc" v-if="item.desc">连招说明：{{ item.desc }}</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { showAvatar, authorLink, iconLink } from "@jx3box/jx3box-common/js/utils";

// 奇穴
import JX3_QIXUE from "@jx3box/jx3box-talent";
import "@jx3box/jx3box-talent/talent.css";
export default {
    props: {
        data: {
            type: Object,
            default: () => {},
        },
        subtype: {
            type: String,
            default: "通用",
        },
    },
    data() {
        return {
            talentDriver: null,
        };
    },
    computed: {
        talent() {
            try {
                return JSON.parse(this.data?.post_meta?.talent);
            } catch (e) {
                return {};
            }
        },
        hasData() {
            return !!this.data?.ID;
        },
        skills() {
            return this.data?.post_meta.data;
        },
    },
    watch: {
        data: {
            deep: true,
            immediate: true,
            handler() {
                if (!this.talentDriver) {
                    this.$nextTick(() => {
                        this.installTalent();
                    });
                } else {
                    this.$nextTick(() => {
                        // this.reloadTalent();
                    });
                }
            },
        },
    },
    methods: {
        showAvatar(val) {
            return showAvatar(val, 88 * 3);
        },
        authorLink,
        iconLink,
        // 初始化奇穴模拟器（此时渲染使用空奇穴模板）
        installTalent() {
            console.log(document.querySelector(`.m-qx-container-${this.data?.ID}`));
            this.talentDriver = new JX3_QIXUE({
                container: `.m-qx-container-${this.data?.ID}`,
                version: this.talent.version,
                xf: this.talent.xf,
                editable: false,
                sq: this.talent.sq,
            });
            // this.reloadTalent();
        },
        nl2br(str) {
            return str.replace(/\n/g, "<br/>");
        },
    },
};
</script>
