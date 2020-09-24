<template>
  <div class="about">
    <h1>顔認識システム</h1>
    <div>
      <div id="button_block">
        <label class="file_select" @click="response_image = null"><span>ファイルを選択してください</span><input type="file" ref="file" @change="setImage"></label>
      </div>
      <div id="action_block">
        <p v-if="response_image">顔検出数：{{ response_people }}</p>
        <input v-else @click="upload" type="button" value="検出">
      </div>
      <div id="target_block">
        <img v-if="response_image" :src="APIdomain + response_image">
        <img v-else :src="this.target_image">
      </div>
    </div>
  </div>
</template>

<script>
    import axios from 'axios';

    export default {
        data: function () {
            return {
                target_image: "",
                response_image: "",
                response_people: 0,
                name: "",
                APIdomain: 'http://127.0.0.1:5000/',
            }
        },
        methods: {
            setImage() {
                const files = this.$refs.file;
                const fileImg = files.files[0];
                // 選択された File の情報を保存しておく
                console.log(this.target_image);
                if (fileImg.type.startsWith("image/")) {
                    this.target_image = window.URL.createObjectURL(fileImg);
                    this.name = fileImg.name;
                    this.type = fileImg.type;
                }
            },
            upload: function () { // アップロード処理
                // FormData を利用して File を POST する
                let formData = new FormData();
                formData.append('file', this.$refs.file.files[0]);
                const config = {
                    headers: {
                        'content-type': 'multipart/form-data'
                    }
                };
                const self = this;
                console.log(this.target_image);
                console.log(formData);
                axios
                    .post(this.APIdomain + 'face', formData, config)
                    .then(function (response) {
                        console.log(response);
                        const response_json = JSON.parse(response['data']);
                        self.response_image = response_json.img;
                        self.response_people = response_json.people;
                    })
                    .catch(function (error) {
                        console.log(error);
                    })
            },
        },
    }
</script>

<style lang="scss">
  .file_select{
    span{
      display: inline-block;
      background: #2c3e50;
      color: #ffffff;
      width: 300px;
      height: 40px;
      text-align: center;
      line-height: 40px;
      border-radius: 10px;
      cursor: pointer;
      font-size: 18px;
    }
    input[type="file"]{
      display: none;
    }
  }
  #button_block{
    text-align: center;
  }
  #action_block{
    display: block;
    height: 100px;
    width: 100%;
    margin-top: 10px;
    p{
      height: 40px;
    }
    input[type="button"] {
      margin-bottom: 10px;
      display: inline-block;
      background: #ff3462;
      color: #ffffff;
      width: 300px;
      height: 40px;
      text-align: center;
      line-height: 40px;
      border-radius: 10px;
      cursor: pointer;
      outline: none;
      border: none;
      font-size: 18px;
    }
  }
  #result_block, #target_block {
    display: inline-block;
    width: 80vw;
    height: auto;
    vertical-align: top;

    img {
      display: block;
      width: 100%;
    }
  }
  #result_block{
    margin-left: 5vw;
  }
</style>