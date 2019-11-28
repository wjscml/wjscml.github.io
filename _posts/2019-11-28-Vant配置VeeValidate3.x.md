---
layout:     post
title:      Vant配置VeeValidate3.x
subtitle:   VeeValidate3.x的使用方法与2.x大相径庭
date:       2019-11-28
author:     Aisopos
header-img: img/zuodeng-01.jpg
catalog: true
tags:
    - Vue
    - 插件
    - 表单验证
---

## 创建validate.js文件 

    import Vue from 'vue'
    import { ValidationProvider, extend, localize } from 'vee-validate'
    import { required, numeric, min } from 'vee-validate/dist/rules' // 验证规则导入
    // import CN from 'vee-validate/dist/locale/zh_CN.json' // 加载语言包

    extend('required', required)
    extend('numeric', numeric)
    extend('min', min)

    localize('zh_CN', {
        name: 'zh_CN',
        names: {
            username: '手机号码',
            password: '密码',
            nationalID: '身份证号',
            patientname: '就诊人姓名',
            phoneNo: '联系方式',
            cardType: '就诊卡号'
        },
        messages: {
            // your messages.
            _default: (field) => `${field}无效`,
            after: (field, { target }) => `${field}必须在${target}之后`,
            alpha_dash: (field) => `${field}能够包含字母数字字符、破折号和下划线`,
            alpha_num: (field) => `${field}只能包含字母数字字符`,
            alpha_spaces: (field) => `${field}只能包含字母字符和空格`,
            alpha: (field) => `${field}只能包含字母字符`,
            before: (field, { target }) => `${field}必须在${target}之前`,
            between: (field, { min, max }) => `${field}必须在${min}与${max}之间`,
            confirmed: (field, { confirmedField }) =>
                `${field}不能和${confirmedField}匹配`,
            credit_card: (field) => `${field}格式错误`,
            date_between: (field, { min, max }) => `${field}必须在${min}和${max}之间`,
            date_format: (field, { format }) => `${field}必须符合${format}格式`,
            decimal: (field, [decimals = '*'] = []) =>
                `${field}必须是数字，且能够保留${
                    decimals === '*' ? '' : decimals}位小数`,
            digits: (field, { length }) =>
                `${field}必须是数字，且精确到${length}位数`,
            dimensions: (field, { width, height }) =>
                `${field}必须在${width}像素与${height}像素之间`,
            email: (field) => `${field}不是一个有效的邮箱`,
            ext: (field) => `${field}不是一个有效的文件`,
            image: (field) => `${field}不是一张有效的图片`,
            included: (field) => `${field}不是一个有效值`,
            integer: (field) => `${field}必须是整数`,
            ip: (field) => `${field}不是一个有效的地址`,
            length: (field, { length, max }) => {
                if (max) {
                    return `${field}长度必须在${length}到${max}之间`
                }
                return `${field}长度必须为${length}`
            },
            max: (field, { length }) => `${field}不能超过${length}个字符`,
            max_value: (field, { max }) => `${field}必须小于或等于${max}`,
            mimes: (field) => `${field}不是一个有效的文件类型`,
            min: (field, { length }) => `${field}必须至少有${length}个字符`,
            min_value: (field, { min }) => `${field}必须大于或等于${min}`,
            excluded: (field) => `${field}不是一个有效值`,
            numeric: (field) => `${field}只能包含数字字符`,
            regex: (field) => `${field}格式无效`,
            required: (field) => `请输入${field}`,
            url: (field) => field + '不是一个有效的url'
        }
    })
    // 手机号码验证
    extend('mobile', {
        message: () => `请输入正确的手机号码`,
        validate: value => value.length === 11 && /^(((13[0-9]{1})|(14[57]{1})|(15[012356789]{1})|(17[03678]{1})|(18[0-9]{1})|(19[89]{1})|(16[6]{1}))+\d{8})$/.test(value)
    })
    // 设置
    Vue.component('ValidationProvider', ValidationProvider)

定义的验证规则要放在localize后面，以防覆盖

## main.js中引用验证规则

    import './common/js/validate.js'

## 页面中的使用方法

    <ValidationObserver ref="form" class="login-form">
        <div class="input-list">
            <ValidationProvider name="username" rules="required|mobile" v-slot="{ errors }">
                <van-field v-model="username" placeholder="请输入手机号码" :error-message="errors[0]" />
            </ValidationProvider>
        </div>
        <div class="input-list">
            <ValidationProvider name="password" rules="required|min:6" v-slot="{ errors }">
                <van-field type="password" v-model="password" placeholder="请输入密码" :error-message="errors[0]" />
            </ValidationProvider>
        </div>
        <div class="login-submit">
            <van-button class="submit-btn" type="info" size="large" color="rgb(0, 122, 255)"
                @click="formSubmit"
            >登 录</van-button>
        </div>
    </ValidationObserver>

    import { ValidationProvider, ValidationObserver } from 'vee-validate'

    components: {
        ValidationProvider,
        ValidationObserver
    }

    formSubmit () {
      this.$refs.form.validate().then(res => {
        if (res) {
          this.login({
            mobileNumber: this.username,
            password: this.password
          })
        }
      })
    }
    
- [官方Demo](https://codesandbox.io/s/veevalidate-30-basic-i18n-bszvu?from-embed)