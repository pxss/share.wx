<template>
  <main class="page" hidden>
    <style>
    /* 把背景色放在body上 bounce滑动不会出现白色断层 */
    body {
      background-color: #f8f8f8;
    }
    </style>

    <div class="page__hd">
      <h1 class="page__title">「 分享生成器 」</h1>
      <p class="page__desc desc-1">“简直就是简易段子手工具”</p>
      <p class="page__desc desc-2">——昌爷</p>
    </div>

    <div class="page__bd">
      <div class="weui-cells__title">
        展示类型
      </div>
      <radio-group name="showType"
        v-model="form.showType"
        :list="showTypeList"
      />

      <fieldset v-show="form.showType === 'link'">
        <div class="weui-cells__title">
          打开跳转到
        </div>
        <div class="weui-cells weui-cells_form">
          <div class="weui-cell">
            <div class="weui-cell__bd">
              <textarea rows="2"
                class="weui-textarea"
                placeholder="“example.tv/天线宝宝/全集”"
                v-model="form.showLink"
              />
            </div>
          </div>
        </div>
      </fieldset>

      <fieldset v-show="form.showType === 'image'">
        <div class="weui-cells weui-cells_form">
          <div class="weui-cell">
            <div class="weui-cell__bd">
              <uploader title="打开展示图片"
                v-model="form.showImage"
              />
            </div>
          </div>
        </div>
      </fieldset>

      <fieldset v-show="form.showType === 'text'">
        <div class="weui-cells__title">
          打开展示文字
        </div>
        <div class="weui-cells weui-cells_form">
          <div class="weui-cell">
            <div class="weui-cell__bd">
              <textarea rows="2"
                class="weui-textarea"
                placeholder="“根本没有”"
                v-model="form.showText"
              />
            </div>
          </div>
        </div>

        <div class="weui-cells__title">
          展示文字大小
        </div>
        <div class="weui-cell no-border">
          <div class="weui-cell__bd">
            <slider :range="[0.1, 3]"
              v-model="form.showTextSize"
            />
          </div>
        </div>
      </fieldset>

      <div class="weui-cells__title">
        分享标题
      </div>
      <div class="weui-cells weui-cells_form">
        <div class="weui-cell">
          <div class="weui-cell__bd">
            <textarea rows="2"
              class="weui-textarea"
              placeholder="“狂吃还能减肥的办法！全网疯转！”"
              v-model="form.shareTitle"
            />
          </div>
        </div>
      </div>

      <div class="weui-cells__title">
        分享描述
      </div>
      <div class="weui-cells weui-cells_form">
        <div class="weui-cell">
          <div class="weui-cell__bd">
            <textarea rows="2"
              class="weui-textarea"
              placeholder="“大家都在围观的直播”"
              v-model="form.shareDesc"
            />
          </div>
        </div>
      </div>

      <div class="weui-cells weui-cells_form">
        <div class="weui-cell">
          <div class="weui-cell__bd">
            <uploader title="分享占位图片"
              v-model="form.shareImage"
            />
          </div>
        </div>
      </div>

      <div class="weui-btn-area">
        <span class="weui-btn weui-btn_primary"
          @click="handleSubmit">
          点击生成
        </span>
      </div>

      <div class="page__ft">
        <p>share.wx.fritx.me</p>
      </div>
    </div>
  </main>
</template>

<script>
import RadioGroup from '~components/RadioGroup'
import Uploader from '~components/Uploader'
import Slider from '~components/Slider'
import loadStats from '~assets/stats'
import wxfn from '~assets/wxfn'
import config from '~/config'
import axios from 'axios'

export default {
  name: 'share-generator',
  components: { Slider, Uploader, RadioGroup },

  head: {
    title: '分享生成器'
  },

  data () {
    return {
      form: {
        shareTitle: '',
        shareDesc: '',
        shareImage: '',
        showLink: '',
        showImage: '',
        showText: '',
        showTextSize: 1,
        showType: 'text',
      },
      showTypeList: [
        { text: '文字', value: 'text' },
        { text: '图片', value: 'image' },
        // { text: '链接', value: 'link' },
        // TODO: 链接类型 专有入口页
      ]
    }
  },

  async mounted () {
    // hack: 解决nuxt 内容比style先加载的bug
    // https://github.com/nuxt/nuxt.js/issues/22
    this.$el.removeAttribute('hidden')

    loadStats()

    const base = location.protocol + '//' + location.host
    await wxfn.config()
    wxfn.share({
      title: '「 分享生成器 」',
      desc: '“简直就是简易段子手工具”',
      link: base,
      imgUrl: `${base}/laoge.jpg`
    })
  },

  methods: {
    async handleSubmit () {
      const form = Object.assign({}, this.form)
      form.shareTitle = form.shareTitle || '惊天爆料'
      form.shareDesc = form.shareDesc || '快来看啊~'
      form.showText = form.showText || '哈哈哈'

      const { data } = await axios
        .post(`${config.apiUrl}/submit`, form)
      this.$router.push(data.url)
    }
  }
}
</script>

<style src="~assets/weui.css"></style>

<style scoped>
.page__hd {
  padding: 40px;
}
.page__title {
  text-align: left;
  font-size: 20px;
  font-weight: 400;
}
.page__desc {
  margin-top: 5px;
  color: #888;
  text-align: left;
  font-size: 14px;
}

fieldset {
  border: none;
}
.page__hd {
  padding: 30px 30px 20px;
}
.page__title {
  margin-bottom: 16px;
  font-size: 22px;
}
.page__ft {
  padding-top: 20px;
}
.desc-1 {
  padding-left: 2.5em;
}
.desc-2 {
  padding-left: 12em;
}
.no-border:before {
  display: none;
}
</style>
