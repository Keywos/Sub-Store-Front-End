<template>
  <div class="page-wrapper">
    <template v-if="isEnvReady">
      <nut-cell-group :title="$t(`apiSettingPage.currentApi.title`)">
        <nut-cell class="cell" center>
          <template v-slot:icon>
            <img :src="icon" alt="" class="auto-reverse backend-icon" />
          </template>
          <template v-slot:title>
            <span class="backend-title">{{
              currentName === ''
                ? $t(`apiSettingPage.apiList.defaultName`)
                : currentName
            }}</span>
          </template>
          <template v-slot:link>
            <span class="backend-version">{{
              `${env.backend} - ${env.version}`
            }}</span>
          </template>
        </nut-cell>
      </nut-cell-group>
    </template>

    <nut-cell-group
      :title="$t(`apiSettingPage.apiList.title`)"
      :desc="$t(`apiSettingPage.apiList.desc`)"
    >
      <nut-cell @click="setCurrent('')">
        <div class="api-list-item">
          <div class="api-item-left">
            <h2>
              {{ $t(`apiSettingPage.apiList.defaultName`) }}
              <nut-tag v-if="currentName === ''" type="primary" plain>{{
                $t(`apiSettingPage.apiList.currentTag`)
              }}</nut-tag>
            </h2>
            <p>{{ defaultAPI }}</p>
          </div>
        </div>
      </nut-cell>

      <nut-cell
        v-for="api in apis"
        :key="api.name"
        @click="setCurrent(api.name)"
      >
        <div class="api-list-item">
          <div class="api-item-left">
            <h2>
              {{ api.name }}
              <nut-tag v-if="currentName === api.name" type="primary" plain
                >{{ $t(`apiSettingPage.apiList.currentTag`) }}
              </nut-tag>
            </h2>
            <p>{{ api.url.slice(0, 20) + '******' }}</p>
          </div>
          <div class="api-item-right">
            <font-awesome-icon
              icon="fa-solid fa-xmark"
              @click.stop="deleteApi(api.name)"
            />
          </div>
        </div>
      </nut-cell>
    </nut-cell-group>

    <nut-cell-group :title="$t(`apiSettingPage.addApi.title`)">
      <div class="add-api-wrapper">
        <nut-input
          class="input"
          v-model="addForm.name"
          :placeholder="$t(`apiSettingPage.addApi.placeholder.name`)"
          type="text"
          input-align="left"
        />
        <nut-input
          class="input"
          v-model="addForm.url"
          :placeholder="$t(`apiSettingPage.addApi.placeholder.url`)"
          type="text"
          input-align="left"
        />
        <nut-button
          class="save-btn"
          type="primary"
          size="mini"
          @click="addApiHandler"
          :disabled="!addForm.name || !addForm.url"
        >
          <font-awesome-icon icon="fa-solid fa-floppy-disk" />
          {{ $t(`apiSettingPage.addApi.btn`) }}
        </nut-button>
      </div>
    </nut-cell-group>

    <p class="desc-text">
      {{ $t(`apiSettingPage.apiSettingDesc`) }}
      <a
        href="https://xream.notion.site/Node-js-render-fork-3334b3943c4f4671b25a24908613e63d"
        target="_blank"
      >
        https://xream.notion.site/Node-js-render-fork-3334b3943c4f4671b25a24908613e63d</a
      >
    </p>
  </div>
</template>

<script setup lang="ts">
  import { useBackend } from '@/hooks/useBackend';
  import { useHostAPI } from '@/hooks/useHostAPI';
  import { ref } from 'vue';

  const { icon, env, isEnvReady } = useBackend();
  const { defaultAPI, currentName, apis, setCurrent, addApi, deleteApi } =
    useHostAPI();

  const addForm = ref<HostAPI>({
    name: '',
    url: '',
  });
  const addApiHandler = async () => {
    if (!addForm.value.name || !addForm.value.url) return;
    const result = await addApi(addForm.value);
    result &&
      (addForm.value = {
        name: '',
        url: '',
      });
  };
</script>

<style scoped>
  .page-wrapper {
    min-height: 100%;
    padding: 16px var(--safe-area-side);
    color: var(--second-text-color);

    .cell {
      box-shadow: none;
      font-weight: bold;

      .backend-icon {
        opacity: 0.64;
        height: 48px;
        aspect-ratio: 1;
      }

      .backend-title {
        margin-left: 12px;
        font-size: 16px;
      }

      .backend-version {
        font-size: 14px;
        color: var(--comment-text-color);
        margin-left: auto;
      }
    }

    .api-list-item {
      width: 100%;
      display: flex;
      justify-content: space-between;
      align-items: center;

      .api-item-left {
        display: flex;
        flex-direction: column;
        gap: 4px;

        :deep(.nut-tag) {
          background: transparent !important;
          height: 20px;
        }

        > h2 {
          display: flex;
          align-items: center;
          gap: 8px;
          font-size: 16px;
          line-height: 20px;
          font-weight: bold;
          color: var(--second-text-color);
        }

        > p {
          font-size: 12px;
          color: var(--comment-text-color);
        }
      }

      .api-item-right {
        font-size: 20px;
        color: var(--comment-text-color);
      }
    }

    .add-api-wrapper {
      margin-top: 8px;
      background: var(--card-color);
      padding: 16px;
      border-radius: 12px;
      display: flex;
      flex-direction: column;
      align-items: end;
      gap: 16px;

      .input {
        background: transparent;
        padding: 8px;
        color: var(--second-text-color);
        font-size: 14px;

        :deep(img) {
          width: 16px;
          height: 16px;
          margin-right: 6px;
          opacity: 0.2;
          filter: brightness(var(--img-brightness));
        }

        &:not(:first-child) {
          margin-top: 8px;
        }
      }
    }

    .desc-text {
      padding: 0 16px;
      font-size: 12px;
      color: var(--comment-text-color);

      > a {
        color: var(--primary-color);
      }
    }
  }
</style>
