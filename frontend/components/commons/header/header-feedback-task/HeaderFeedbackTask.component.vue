<template>
  <BaseTopbarBrand>
    <BaseBreadcrumbs
      v-if="breadcrumbs.length"
      :breadcrumbs="breadcrumbs"
      :copy-button="copyButton"
      @breadcrumb-action="$emit('breadcrumb-action', $event)"
    />
    <template v-if="datasetId">
      <BaseButton
        class="header__button small"
        @on-click="onClickTrain"
        v-if="isAdminRole && showTrainButton"
      >
        <svgicon name="code" width="20" height="20" />Train
      </BaseButton>
      <NuxtLink
        :to="{ name: 'dataset-id-settings', params: { id: this.datasetId } }"
      >
        <DatasetSettingsIconFeedbackTaskComponent
          v-if="datasetId"
          :datasetId="datasetId"
        />
      </NuxtLink>
    </template>
    <user />
  </BaseTopbarBrand>
</template>

<script>
export default {
  name: "HeaderFeedbaskTaskComponent",
  props: {
    datasetId: {
      type: String,
      required: true,
    },
    breadcrumbs: {
      type: Array,
      default: () => [],
    },
    showTrainButton: {
      type: Boolean,
      default: () => false,
    },
  },
  data() {
    return {
      copyButton: false,
    };
  },
  computed: {
    isAdminRole() {
      return this.$auth.user.role === "admin";
    },
  },
  methods: {
    onClickTrain() {
      this.$emit("on-click-train");
    },
  },
};
</script>

<style lang="scss" scoped>
$header-button-color: #262a2e;
.header__button {
  background: $header-button-color;
  color: palette(white);
  margin-right: $base-space;
  padding: 10px 12px 10px 10px;
  font-weight: 600;
  @include font-size(14px);
  box-shadow: $shadow-200;
  &:hover {
    background: lighten($header-button-color, 3%);
  }
  svg {
    fill: palette(white);
  }
}
</style>
