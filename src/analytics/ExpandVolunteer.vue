<template>
    <div class="ExpandVolunteer expand-volunteer">
        <div class="expand-volunteer__empty"></div>
        <div class="expand-volunteer__content">
            <div class="expand-volunteer__address">
                {{ volunteer.address }}
            </div>
            <div class="expand-volunteer__phone">
                {{ volunteer.phone }}
            </div>
            <div class="expand-volunteer__skills"
                 v-if="volunteer.skills.length"
            >
                <div class="expand-volunteer__skills-title">
                    Skills:
                </div>
                <div class="expand-volunteer__skills-box">
                    <div class="expand-volunteer__skills-item"
                         v-for="item in volunteer.skills"
                         :key="item.name"
                    >
                        {{ item.name }}
                        <span class="expand-volunteer__skills-count"
                              v-if="item.skillLevel">
                            {{ item.skillLevel > 9 ? '9+' : item.skillLevel }}
                        </span>
                    </div>
                </div>
            </div>
            <div class="expand-volunteer__no-skills"
                 v-else
            >
                The volunteer hasn't yet input any skills or acquired any from volunteering.
            </div>
        </div>
    </div>
</template>

<script>
const ExpandVolunteer = {
    name: 'expand-volunteer',
    props: {
        volunteer: {
            type: Object,
            require: true,
        },
    },
};
export default ExpandVolunteer;
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
    @import '../../../../sass/variables/td_theme';

    .expand-volunteer {
        display: flex;
        width: 100%;
        &__empty {
            width: 4%;
        }
        &__content {
            padding-bottom: 8px;
        }
        &__address {
            @include fnt($text-light, 12px, $weight-light, left);
            line-height: 1;
        }
        &__phone {
            @include fnt($text, 12px, $weight-semibold, left);
            padding: 12px 0;
            line-height: 1;
        }

        &__skills {
            display: flex;
            align-items: center;
        }
        &__skills-title{
            @include fnt($text, 12px, $weight-semibold, left);
            text-transform: uppercase;
            padding-right: 8px;
        }
        &__no-skills {
            @include fnt($text, 12px, $weight-light, left);
        }
        &__skills-box {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
        }
        &__skills-item {
            @include fnt($text-invert, 10px, $weight-light, left);
            align-items: center;
            background-color: $blue;
            border-radius: 3px;
            display: flex;
            flex: 0 1 auto;
            justify-content: center;
            margin: 2px;
            padding: 2px 4px;
            white-space: nowrap;
        }
        &__skills-count {
            @include fnt($blue, 8px, $weight-light, center);
            align-items: center;
            background-color: $white;
            border-radius: 50%;
            display: flex;
            flex: 0 0 auto;
            height: 12px;
            justify-content: center;
            margin-left: 2px;
            width: 12px;
        }
    }

</style>