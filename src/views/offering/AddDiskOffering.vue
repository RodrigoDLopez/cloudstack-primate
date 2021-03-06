// Licensed to the Apache Software Foundation (ASF) under one
// or more contributor license agreements.  See the NOTICE file
// distributed with this work for additional information
// regarding copyright ownership.  The ASF licenses this file
// to you under the Apache License, Version 2.0 (the
// "License"); you may not use this file except in compliance
// with the License.  You may obtain a copy of the License at
//
//   http://www.apache.org/licenses/LICENSE-2.0
//
// Unless required by applicable law or agreed to in writing,
// software distributed under the License is distributed on an
// "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
// KIND, either express or implied.  See the License for the
// specific language governing permissions and limitations
// under the License.

<template>
  <div class="form-layout">
    <a-spin :spinning="loading">
      <a-form
        :form="form"
        @submit="handleSubmit"
        layout="vertical">
        <a-form-item :label="$t('label.name')">
          <a-input
            v-decorator="['name', {
              rules: [{ required: true, message: $t('message.error.required.input') }]
            }]"
            :placeholder="this.$t('label.name')"/>
        </a-form-item>
        <a-form-item :label="$t('label.displaytext')">
          <a-input
            v-decorator="['displaytext', {
              rules: [{ required: true, message: $t('message.error.required.input') }]
            }]"
            :placeholder="this.$t('label.displaytext')"/>
        </a-form-item>
        <a-form-item :label="$t('label.storagetype')">
          <a-radio-group
            v-decorator="['storagetype', {
              initialValue: this.storageType
            }]"
            buttonStyle="solid"
            @change="selected => { this.handleStorageTypeChange(selected.target.value) }">
            <a-radio-button value="shared">
              {{ $t('label.shared') }}
            </a-radio-button>
            <a-radio-button value="local">
              {{ $t('label.local') }}
            </a-radio-button>
          </a-radio-group>
        </a-form-item>
        <a-form-item :label="$t('label.provisioningtype')">
          <a-radio-group
            v-decorator="['provisioningtype', {
              initialValue: this.provisioningType
            }]"
            buttonStyle="solid"
            @change="selected => { this.handleProvisioningTypeChange(selected.target.value) }">
            <a-radio-button value="thin">
              {{ $t('label.provisioningtype.thin') }}
            </a-radio-button>
            <a-radio-button value="sparse">
              {{ $t('label.provisioningtype.sparse') }}
            </a-radio-button>
            <a-radio-button value="fat">
              {{ $t('label.provisioningtype.fat') }}
            </a-radio-button>
          </a-radio-group>
        </a-form-item>
        <a-form-item :label="$t('label.customdisksize')">
          <a-switch v-decorator="['customdisksize', { initialValue: this.isCustomDiskSize }]" :checked="this.isCustomDiskSize" @change="val => { this.isCustomDiskSize = val }" />
        </a-form-item>
        <a-form-item :label="$t('label.disksize')" v-if="!this.isCustomDiskSize">
          <a-input
            v-decorator="['disksize', {
              rules: [
                { required: true, message: $t('message.error.required.input') },
                {
                  validator: (rule, value, callback) => {
                    if (value && (isNaN(value) || value <= 0)) {
                      callback(this.$t('message.error.number'))
                    }
                    callback()
                  }
                }
              ]
            }]"
            :placeholder="this.$t('label.disksize')"/>
        </a-form-item>
        <a-form-item :label="$t('label.qostype')">
          <a-radio-group
            v-decorator="['qostype', {
              initialValue: this.qosType
            }]"
            buttonStyle="solid"
            @change="selected => { this.handleQosTypeChange(selected.target.value) }">
            <a-radio-button value="">
              {{ $t('label.none') }}
            </a-radio-button>
            <a-radio-button value="hypervisor">
              {{ $t('label.hypervisor') }}
            </a-radio-button>
            <a-radio-button value="storage">
              {{ $t('label.storage') }}
            </a-radio-button>
          </a-radio-group>
        </a-form-item>
        <a-form-item :label="$t('label.diskbytesreadrate')" v-if="this.qosType === 'hypervisor'">
          <a-input
            v-decorator="['diskbytesreadrate', {
              rules: [{
                validator: (rule, value, callback) => {
                  if (value && (isNaN(value) || value <= 0)) {
                    callback(this.$t('message.error.number'))
                  }
                  callback()
                }
              }]
            }]"
            :placeholder="this.$t('label.diskbytesreadrate')"/>
        </a-form-item>
        <a-form-item :label="$t('label.diskbyteswriterate')" v-if="this.qosType === 'hypervisor'">
          <a-input
            v-decorator="['diskbyteswriterate', {
              rules: [{
                validator: (rule, value, callback) => {
                  if (value && (isNaN(value) || value <= 0)) {
                    callback(this.$t('message.error.number'))
                  }
                  callback()
                }
              }]
            }]"
            :placeholder="this.$t('label.diskbyteswriterate')"/>
        </a-form-item>
        <a-form-item :label="$t('label.diskiopsreadrate')" v-if="this.qosType === 'hypervisor'">
          <a-input
            v-decorator="['diskiopsreadrate', {
              rules: [{
                validator: (rule, value, callback) => {
                  if (value && (isNaN(value) || value <= 0)) {
                    callback(this.$t('message.error.number'))
                  }
                  callback()
                }
              }]
            }]"
            :placeholder="this.$t('label.diskiopsreadrate')"/>
        </a-form-item>
        <a-form-item :label="$t('label.diskiopswriterate')" v-if="this.qosType === 'hypervisor'">
          <a-input
            v-decorator="['diskiopswriterate', {
              rules: [{
                validator: (rule, value, callback) => {
                  if (value && (isNaN(value) || value <= 0)) {
                    callback(this.$t('message.error.number'))
                  }
                  callback()
                }
              }]
            }]"
            :placeholder="this.$t('label.diskiopswriterate')"/>
        </a-form-item>
        <a-form-item :label="$t('label.iscustomizeddiskiops')" v-if="this.qosType === 'storage'">
          <a-switch v-decorator="['iscustomizeddiskiops']" :checked="this.isCustomizedDiskIops" @change="val => { this.isCustomizedDiskIops = val }" />
        </a-form-item>
        <a-form-item :label="$t('label.diskiopsmin')" v-if="this.qosType === 'storage' && !this.isCustomizedDiskIops">
          <a-input
            v-decorator="['diskiopsmin', {
              rules: [{
                validator: (rule, value, callback) => {
                  if (value && (isNaN(value) || value <= 0)) {
                    callback(this.$t('message.error.number'))
                  }
                  callback()
                }
              }]
            }]"
            :placeholder="this.$t('label.diskiopsmin')"/>
        </a-form-item>
        <a-form-item :label="$t('label.diskiopsmax')" v-if="this.qosType === 'storage' && !this.isCustomizedDiskIops">
          <a-input
            v-decorator="['diskiopsmax', {
              rules: [{
                validator: (rule, value, callback) => {
                  if (value && (isNaN(value) || value <= 0)) {
                    callback(this.$t('message.error.number'))
                  }
                  callback()
                }
              }]
            }]"
            :placeholder="this.$t('label.diskiopsmax')"/>
        </a-form-item>
        <a-form-item :label="$t('label.hypervisorsnapshotreserve')" v-if="this.qosType === 'storage'">
          <a-input
            v-decorator="['hypervisorsnapshotreserve', {
              rules: [{
                validator: (rule, value, callback) => {
                  if (value && (isNaN(value) || value <= 0)) {
                    callback(this.$t('message.error.number'))
                  }
                  callback()
                }
              }]
            }]"
            :placeholder="this.$t('label.hypervisorsnapshotreserve')"/>
        </a-form-item>
        <a-form-item :label="$t('label.writecachetype')">
          <a-radio-group
            v-decorator="['writecachetype', {
              initialValue: this.writeCacheType
            }]"
            buttonStyle="solid"
            @change="selected => { this.handleWriteCacheTypeChange(selected.target.value) }">
            <a-radio-button value="none">
              {{ $t('label.nodiskcache') }}
            </a-radio-button>
            <a-radio-button value="writeback">
              {{ $t('label.writeback') }}
            </a-radio-button>
            <a-radio-button value="writethrough">
              {{ $t('label.writethrough') }}
            </a-radio-button>
          </a-radio-group>
        </a-form-item>
        <a-form-item :label="$t('label.storagetags')" v-if="this.isAdmin()">
          <a-select
            mode="tags"
            v-decorator="['tags', {}]"
            showSearch
            optionFilterProp="children"
            :filterOption="(input, option) => {
              return option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0
            }"
            :loading="storageTagLoading"
            :placeholder="this.$t('label.tags')"
            v-if="this.isAdmin()">
            <a-select-option v-for="(opt) in this.storageTags" :key="opt.name">
              {{ opt.name || opt.description }}
            </a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item :label="$t('label.ispublic')" v-show="this.isAdmin()">
          <a-switch v-decorator="['ispublic', {initialValue: this.isPublic}]" :checked="this.isPublic" @change="val => { this.isPublic = val }" />
        </a-form-item>
        <a-form-item :label="$t('label.domainid')" v-if="!this.isPublic">
          <a-select
            mode="multiple"
            v-decorator="['domainid', {
              rules: [
                {
                  required: true,
                  message: $t('message.error.select')
                }
              ]
            }]"
            showSearch
            optionFilterProp="children"
            :filterOption="(input, option) => {
              return option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0
            }"
            :loading="domainLoading"
            :placeholder="this.$t('label.domainid')">
            <a-select-option v-for="(opt, optIndex) in this.domains" :key="optIndex">
              {{ opt.name || opt.description }}
            </a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item :label="$t('label.zoneid')">
          <a-select
            id="zone-selection"
            mode="multiple"
            v-decorator="['zoneid', {
              rules: [
                {
                  validator: (rule, value, callback) => {
                    if (value && value.length > 1 && value.indexOf(0) !== -1) {
                      callback(this.$t('label.error.zone.combined'))
                    }
                    callback()
                  }
                }
              ]
            }]"
            showSearch
            optionFilterProp="children"
            :filterOption="(input, option) => {
              return option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0
            }"
            :loading="zoneLoading"
            :placeholder="this.$t('label.zoneid')">
            <a-select-option v-for="(opt, optIndex) in this.zones" :key="optIndex">
              {{ opt.name || opt.description }}
            </a-select-option>
          </a-select>
        </a-form-item>
      </a-form>
      <div :span="24" class="action-button">
        <a-button @click="closeAction">{{ this.$t('label.cancel') }}</a-button>
        <a-button :loading="loading" type="primary" @click="handleSubmit">{{ this.$t('label.ok') }}</a-button>
      </div>
    </a-spin>
  </div>
</template>

<script>
import { api } from '@/api'

export default {
  name: 'AddDiskOffering',
  props: {
  },
  components: {
  },
  data () {
    return {
      storageType: 'shared',
      provisioningType: 'thin',
      isCustomDiskSize: true,
      qosType: '',
      isCustomizedDiskIops: false,
      writeCacheType: 'none',
      selectedDomains: [],
      selectedZones: [],
      storageTags: [],
      storageTagLoading: false,
      isPublic: true,
      domains: [],
      domainLoading: false,
      zones: [],
      zoneLoading: false,
      loading: false
    }
  },
  beforeCreate () {
    this.form = this.$form.createForm(this)
  },
  created () {
    this.zones = [
      {
        id: null,
        name: this.$t('label.all.zone')
      }
    ]
  },
  mounted () {
    this.fetchData()
    this.isPublic = this.isAdmin()
  },
  methods: {
    fetchData () {
      this.fetchDomainData()
      this.fetchZoneData()
      if (this.isAdmin()) {
        this.fetchStorageTagData()
      }
    },
    isAdmin () {
      return ['Admin'].includes(this.$store.getters.userInfo.roletype)
    },
    arrayHasItems (array) {
      return array !== null && array !== undefined && Array.isArray(array) && array.length > 0
    },
    fetchDomainData () {
      const params = {}
      params.listAll = true
      params.details = 'min'
      this.domainLoading = true
      api('listDomains', params).then(json => {
        const listDomains = json.listdomainsresponse.domain
        this.domains = this.domains.concat(listDomains)
      }).finally(() => {
        this.domainLoading = false
      })
    },
    fetchZoneData () {
      const params = {}
      params.listAll = true
      this.zoneLoading = true
      api('listZones', params).then(json => {
        const listZones = json.listzonesresponse.zone
        this.zones = this.zones.concat(listZones)
      }).finally(() => {
        this.zoneLoading = false
      })
    },
    fetchStorageTagData () {
      const params = {}
      params.listAll = true
      this.storageTagLoading = true
      api('listStorageTags', params).then(json => {
        const tags = json.liststoragetagsresponse.storagetag
        if (this.arrayHasItems(tags)) {
          for (var i in tags) {
            var tag = {}
            tag.id = tags[i].name
            tag.name = tags[i].name
            this.storageTags.push(tag)
          }
        }
      }).finally(() => {
        this.storageTagLoading = false
      })
    },
    handleStorageTypeChange (val) {
      this.storageType = val
    },
    handleProvisioningTypeChange (val) {
      this.provisioningType = val
    },
    handleQosTypeChange (val) {
      this.qosType = val
    },
    handleWriteCacheTypeChange (val) {
      this.writeCacheType = val
    },
    handleSubmit (e) {
      e.preventDefault()
      this.form.validateFields((err, values) => {
        if (err) {
          return
        }
        var params = {
          isMirrored: false,
          name: values.name,
          displaytext: values.displaytext,
          storageType: values.storagetype,
          cacheMode: values.writecachetype,
          provisioningType: values.provisioningtype,
          customized: values.customdisksize
        }
        if (values.customdisksize !== true) {
          params.disksize = values.disksize
        }
        if (values.qostype === 'storage') {
          var customIops = values.iscustomizeddiskiops === true
          params.customizediops = customIops
          if (!customIops) {
            if (values.diskiopsmin != null && values.diskiopsmin.length > 0) {
              params.miniops = values.diskiopsmin
            }
            if (values.diskiopsmax != null && values.diskiopsmax.length > 0) {
              params.maxiops = values.diskiopsmax
            }
            if (values.hypervisorsnapshotreserve != null && values.hypervisorsnapshotreserve.length > 0) {
              params.hypervisorsnapshotreserve = values.hypervisorsnapshotreserve
            }
          }
        } else if (values.qostype === 'hypervisor') {
          if (values.diskbytesreadrate != null && values.diskbytesreadrate.length > 0) {
            params.bytesreadrate = values.diskbytesreadrate
          }
          if (values.diskbyteswriterate != null && values.diskbyteswriterate.length > 0) {
            params.byteswriterate = values.diskbyteswriterate
          }
          if (values.diskiopsreadrate != null && values.diskiopsreadrate.length > 0) {
            params.iopsreadrate = values.diskiopsreadrate
          }
          if (values.diskiopswriterate != null && values.diskiopswriterate.length > 0) {
            params.iopswriterate = values.diskiopswriterate
          }
        }
        if (values.tags != null && values.tags.length > 0) {
          var tags = values.tags.join(',')
          params.tags = tags
        }
        if (values.ispublic !== true) {
          var domainIndexes = values.domainid
          var domainId = null
          if (domainIndexes && domainIndexes.length > 0) {
            var domainIds = []
            for (var i = 0; i < domainIndexes.length; i++) {
              domainIds = domainIds.concat(this.domains[domainIndexes[i]].id)
            }
            domainId = domainIds.join(',')
          }
          if (domainId) {
            params.domainid = domainId
          }
        }
        var zoneIndexes = values.zoneid
        var zoneId = null
        if (zoneIndexes && zoneIndexes.length > 0) {
          var zoneIds = []
          for (var j = 0; j < zoneIndexes.length; j++) {
            zoneIds = zoneIds.concat(this.zones[zoneIndexes[j]].id)
          }
          zoneId = zoneIds.join(',')
        }
        if (zoneId) {
          params.zoneid = zoneId
        }
        api('createDiskOffering', params).then(json => {
          this.$message.success(this.$t('message.disk.offering.created', { name: values.name }))
        }).catch(error => {
          this.$notifyError(error)
        }).finally(() => {
          this.loading = false
          this.$emit('refresh-data')
          this.closeAction()
        })
      })
    },
    closeAction () {
      this.$emit('close-action')
    }
  }
}
</script>

<style scoped lang="scss">
  .form-layout {
    width: 80vw;

    @media (min-width: 800px) {
      width: 430px;
    }
  }

  .action-button {
    text-align: right;

    button {
      margin-right: 5px;
    }
  }
</style>
