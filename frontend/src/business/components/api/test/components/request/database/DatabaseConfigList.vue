<template>
  <div class="database-config-list">

    <el-table border :data="tableData" class="adjust-table table-content"
              @row-click="handleView">

      <el-table-column type="expand">
        <template slot-scope="props">
          <ms-database-from :callback="editConfig" :config="props.row"/>
        </template>
      </el-table-column>
      <el-table-column prop="name" :label="$t('api_test.request.sql.dataSource')" show-overflow-tooltip/>
      <el-table-column prop="driver" :label="$t('api_test.request.sql.database_driver')"  show-overflow-tooltip/>
      <el-table-column prop="dbUrl" :label="$t('api_test.request.sql.database_url')" show-overflow-tooltip/>
      <el-table-column prop="username" :label="$t('api_test.request.sql.username')"  show-overflow-tooltip/>
      <el-table-column prop="poolMax" :label="$t('api_test.request.sql.pool_max')" show-overflow-tooltip/>
      <el-table-column prop="timeout" :label="$t('api_test.request.sql.query_timeout')"  show-overflow-tooltip/>

      <el-table-column :label="$t('commons.operating')" min-width="100">
        <template v-slot:default="scope">
          <ms-table-operator-button :is-tester-permission="true" :tip="$t('commons.copy')" icon="el-icon-document-copy" type="success" @exec="handleCopy(scope.$index, scope.row)"/>
          <ms-table-operator-button :isTesterPermission="true" :tip="$t('commons.delete')" icon="el-icon-delete" type="danger" @exec="handleDelete(scope.$index)"/>
        </template>
      </el-table-column>

    </el-table>

  </div>
</template>

<script>

    import {DatabaseConfig} from "../../../model/ScenarioModel";
    import MsTableOperator from "../../../../../common/components/MsTableOperator";
    import MsTableOperatorButton from "../../../../../common/components/MsTableOperatorButton";
    import {getUUID} from "../../../../../../../common/js/utils";
    import MsDatabaseFrom from "./DatabaseFrom";

    export default {
      name: "MsDatabaseConfigList",
      components: {MsDatabaseFrom, MsTableOperatorButton, MsTableOperator},
      props: {
        tableData: Array,
        isReadOnly: {
          type: Boolean,
          default: false
        }
      },
      data() {
        return {
          drivers: DatabaseConfig.DRIVER_CLASS,
          result: {},
        }
      },
      methods: {
        handleView() {
        },
        handleEdit(config) {
          this.$refs.databaseConfigEdit.open(config);
        },
        editConfig(config) {
          let index = 0;
          for (let i in this.tableData) {
            let item = this.tableData[i];
            if (item.name === config.name && item.id != config.id) {
              this.$warning(this.$t('commons.already_exists'));
              return;
            }
            if (item.id === config.id) {
              index = i;
            }
          }
          Object.assign(this.tableData[index], config);
          this.$success(this.$t('commons.save_success'));
        },
        handleDelete(index) {
          this.tableData.splice(index, 1);
        },
        handleCopy(index, config) {
          let copy = {};
          Object.assign(copy, config);
          copy.id = getUUID();
          copy.name = this.getNoRepeatName(copy.name);
          this.tableData.splice(index + 1, 0, copy);
        },
        getNoRepeatName(name) {
          for (let i in this.tableData) {
            if (this.tableData[i].name === name) {
              return this.getNoRepeatName(name + ' copy');
            }
          }
          return name;
        },
      }
    }
</script>

<style scoped>

  .addButton {
    float: right;
  }

</style>
