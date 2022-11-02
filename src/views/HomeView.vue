<template>
  <main class="input-test">
    <div class="ctrl-block">
      <el-button type="primary" size="default" @click="openCreate">添加表单</el-button>
    </div>
    <div class="form-block">
      <el-form label-position="top" :model="createForm" :inline="false" size="small">
        <template v-for="(item, i) in formList" :key="item.valueName">
          <el-form-item :prop="item.valueName">
            <template #label>
              {{ i + 1 }}、{{ item.label }}：
              <span class="form-info">（{{ item.info }}）</span>
              <el-tooltip :content="item.tip" placement="right" effect="light">
                <el-button circle>!</el-button>
              </el-tooltip>
              <el-button type="text" @click="delItem(i)">删除</el-button>
            </template>
            <el-radio-group v-if="item.type === 0">
              <el-radio v-for="radio in item.radioLabels" :key="radio.id" :label="radio.id">
                {{ radio.name }}
              </el-radio>
            </el-radio-group>
            <el-input v-else-if="item.type === 1" type="textarea" :placeholder="item.placeholder" />
          </el-form-item>
        </template>
      </el-form>
    </div>

    <el-dialog v-model="createDialog" title="添加表单" width="500px">
      <el-form
        :model="createForm"
        ref="createFormRef"
        label-width="90px"
        :inline="false"
        size="small"
      >
        <el-form-item label="表单类型：" prop="type">
          <el-select v-model="createForm.type">
            <el-option
              v-for="item in typeOptions"
              :key="item.id"
              :label="item.name"
              :value="item.id"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="标题：" prop="label">
          <el-input v-model="createForm.label" />
        </el-form-item>
        <el-form-item label="说明：" prop="info">
          <el-input type="textarea" v-model="createForm.info" />
        </el-form-item>
        <el-form-item label="提示：" prop="tip">
          <el-input type="textarea" v-model="createForm.tip" />
        </el-form-item>
        <template v-if="createForm.type === 0">
          <el-form-item label="单选框数量：" prop="radioNum">
            <el-input-number
              v-model="createForm.radioNum"
              :min="0"
              :controls="false"
              @blur="genRadioInput"
            />
          </el-form-item>
          <el-form-item
            label="单选框名称："
            prop="radioLabels"
            v-if="
              createForm.radioNum !== undefined &&
              createForm.radioNum !== '' &&
              createForm.radioNum > 0
            "
          >
            <div class="radio-name-input-block">
              <div v-for="item in createForm.radioLabels" :key="item.id">
                {{ item.id + 1 }}. <el-input style="width: 100px" v-model="item.name" />
              </div>
            </div>
          </el-form-item>
        </template>
        <template v-else-if="createForm.type === 1">
          <el-form-item label="输入框提示：" prop="placeholder">
            <el-input v-model="createForm.placeholder" />
          </el-form-item>
        </template>
      </el-form>

      <template #footer>
        <span class="dialog-footer">
          <el-button @click="createDialog = false">取消</el-button>
          <el-button type="primary" @click="doAdd"> 确定 </el-button>
        </span>
      </template>
    </el-dialog>
  </main>
</template>
<script setup lang="ts">
  import { nextTick, reactive, ref } from 'vue';
  import type { FormInstance } from 'element-plus';

  interface CreateForm {
    valueName: string;
    type: string | number;
    label: string;
    tip: string;
    info: string;
    placeholder?: string;
    radioNum?: string | number | undefined;
    radioLabels?: { id: number; name: string }[];
  }

  const typeOptions = [
    {
      id: 0,
      name: '单选',
    },
    {
      id: 1,
      name: '输入框',
    },
  ];
  const createDialog = ref(false);
  let createForm: CreateForm = reactive({
    valueName: '',
    type: '',
    label: '',
    tip: '',
    info: '',
    placeholder: '',
    radioNum: 0,
    radioLabels: [],
  });
  const formList: CreateForm[] = reactive([]);
  const createFormRef = ref<FormInstance>();
  const openCreate = () => {
    createDialog.value = true;
    nextTick(() => {
      createFormRef?.value?.resetFields();
    });
  };

  const genRadioInput = () => {
    if (
      createForm.radioNum !== undefined &&
      createForm.radioNum !== '' &&
      createForm.radioNum > 0
    ) {
      createForm.radioLabels = new Array(createForm.radioNum).fill('0').map((v, i) => {
        return {
          id: i,
          name: '',
        };
      });
    }
  };

  const doAdd = () => {
    createForm.valueName = guid();
    formList.push(JSON.parse(JSON.stringify(createForm)));
  };

  const delItem = (index: number) => {
    formList.splice(index, 1);
  };

  const guid = () => {
    return 'xxxxxxxxxxxx4xxxyxxxxxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
      var r = (Math.random() * 16) | 0,
        v = c == 'x' ? r : (r & 0x3) | 0x8;
      return v.toString(16);
    });
  };
</script>
<style lang="scss">
  .form-block {
    .form-info {
      color: #ddd;
    }
  }
</style>
