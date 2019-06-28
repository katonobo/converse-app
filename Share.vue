<template>
  <div class="tapioka">
    <h2 class="title is-4"  style="font-weight:bold;">タピオカ</h2>
        <!-- bulmaのレイアウト「colums」を使用 https://bulma.io/documentation/columns/basics/ -->
        <div class="columns"> 
          <div class="column">
            <p class="title is-5">①単位</p>
            <div class="field is-grouped is-grouped-centered">
            <!-- v-modelで連想配列tapiokaDataにoptionで選ばれたvalue(今回ならtapiokaKal)を入れる -->
            <!-- b-selectはbuefyのこのURL参考 https://buefy.org/documentation/select -->
              <b-select v-model="tapiokaData" placeholder="Select a character">
                <option :value="tapiokaKal">カロリー</option>
              </b-select>
            </div>
          </div>
          <div class="column is-1">
            <i class="fas fa-times"></i>
          </div>
          <div class="column">
            <p class="title is-5">②比較するもの</p>
            <!-- 入力画面の見栄えをよくするために空のデータで選択肢が見れるようにしている -->
            <div v-if="tapiokaData.category == ''">
              <div class="field is-grouped is-grouped-centered">
                  <b-select v-model="targetData" placeholder="Select a character">
                  </b-select>
              </div>
            </div>
            <!-- 選択されたカテゴリーのデータを表示 -->
            <div v-if="tapiokaData.category == 'kal'">
              <div class="field is-grouped is-grouped-centered">
                <!-- v-modelで連想配列tagetDataにoptionで選択されたデータを入れる -->
                <!-- 今後いくつカロリーのデータ入るかわからないので、v-forのループで表示させている -->
                  <b-select v-model="targetData" placeholder="Select a character">
                    <option v-for="k in kals" :key="k.kals" :value="k">
                      <span>{{k.name}}</span>
                    </option>
                  </b-select>
              </div>
            </div>
          </div>
          <div class="column is-1">
            <i class="fas fa-times"></i>
          </div>
          <div class="column">
            <p class="title is-5">③数字</p>
            <!-- 選択したデータの基本的な数値と単位を表示。inputを使って変更できるようにしている -->
            <div class="field is-grouped is-grouped-centered">
              <b-input style="width:70px; text-align:center;" v-model="targetData.number"></b-input>
              <span style="padding-top:10px;">{{targetData.unit}}</span>
            </div>
          </div>
        </div>
    <div class="result">
      <b-button @click="cal" class="is-info">結果を見る</b-button>
      <div v-if="err" style="color:red;">未選択の項目があります</div>
      <p v-if="calAnswer" class="choice_result">{{targetData.name}} × {{tapiokaData.tapioka}}</p>
        <div v-if="calAnswer" class="result_card">
            <p>{{targetData.name}} <span style="font-size:1.1em; font-weight:bold;">{{targetData.number}}</span> {{targetData.unit}} は、
            {{tapiokaData.tapioka}} <span style="font-size:1.2em; font-weight:bold;">{{answer1}}</span> {{tapiokaData.tapioka_unit}}、
            {{tapiokaData.tapiokamilk}} <span style="font-size:1.2em; font-weight:bold;">{{answer2}}</span> {{tapiokaData.tapiokamilk_unit}} 相当の{{targetData.category}}です </p>
        </div>
        <p v-if="calAnswer" class="share_button"><b-button @click="share" type="is-info" icon-left="twitter" outlined>シェア</b-button></p>
        <p v-if="calAnswer" class="title is-6">結果をシェアしよう！</p>
    </div>

    <div class="explain">
      <hr>
      <h2>タピオカの重量とカロリーについて</h2>
      <p>タピオカは乾燥状態だと一粒0.7グラムで、茹でた後だと1グラムになります。</p>
      <p>カロリーは、タピオカ自体が100グラムで346kcal、ミルクティー500mlで700kcalになります。</p>
      <p>*数値はあくまで目安でお考えください。</p>
      <h2>登録データ</h2>
      <!-- 登録しているカロリーの比較対象のデータをv-forで表示 -->
      <span v-for="k in kals" :key="k.kals">
        <b-tag>{{ k.name }}</b-tag>
      </span>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
export default {
  name: 'share',
  data() {
    return {
      calAnswer: false,
      err: null,
      targetData: {category:''},
      tapiokaData: {category:''},
      tapiokaKal: {tapioka:"タピオカ", tapiokamilk:"タピオカミルクティー", tapioka_unit:"粒",tapiokamilk_unit:"杯",category:"kal", tapioka_kal:3.46, tapiokamilk_kal:700,},
      kals:{
        アイスクリーム: {name:"アイスクリーム", unit:"個", category:"カロリー", number:'1', kal:244,},
        ウォーキング: {name:"ウォーキング", unit:"時間", category:"カロリー消費", number:'1', kal:189},
        おにぎり: {name:"おにぎり", unit:"個", category:"カロリー", number:'1', kal:215,},
      }
    }
  },
  computed:{
    answer1(){
    //計算結果１(タピオカとの比較) ＝ (比較対象の数 * 比較対象のカロリー) / タピオカのカロリー
      return ((this.targetData.number * this.targetData.kal) / this.tapiokaData.tapioka_kal).toFixed(0)
    },
    answer2(){
    //計算結果2(タピオカミルクティーとの比較) ＝ (比較対象の数 * 比較対象のカロリー) / タピオカミルクティのカロリー
      var cal = (this.targetData.number * this.targetData.kal) / this.tapiokaData.tapiokamilk_kal
      var calResult = Math.round(cal * 100) / 100
      return calResult
    },
    answer3(){
    //計算結果3(タピオカミルクティー一杯との比較) = タピオカミルクティのカロリー / 比較対象のカロリー
      return (this.tapiokaData.tapiokamilk_kal / this.targetData.kal).toFixed(1)
    },
  },
  methods:{
    cal(){
      if(this.tapiokaData.category !='' && this.targetData.number != '' ){
        this.calAnswer = true
        this.err = false
      }else{
        this.err = true
      }
    },
    share(){
      var shareURL = 'https://twitter.com/intent/tweet?text='
       +this.targetData.name + this.targetData.number + this.targetData.unit + "は、" +this.tapiokaData.tapioka + this.answer1 + this.tapiokaData.tapioka_unit +  "、" +this.tapiokaData.tapiokamilk + this.answer2 + this.tapiokaData.tapiokamilk_unit +  "相当の" + this.targetData.category + "です。"
       + "%20%23おもしろ単位換算メーカー%20%23タピオカ"
       + '&url=' + "https://conversion.ameneko.com/";
      location.href = shareURL
    }
  }

}
</script>

<style scoped>
.tapioka{
  margin: auto;
  max-width: 650px;
  text-align: center;
}
.target1_value{
  width: 40px;
}
.choice_result{
  padding-top:20px;
  padding-bottom:10px;
  font-weight: bold;
}
.result_card{
    margin : 10px;
    padding : 1em 1.5em ;
    line-height : 1.8 ;
    border : dotted 4px #333 ; 
    font-weight: bold;
}
.explain{
  margin-top: 100px;
}
.tag{
  margin:3px;
}
</style>
