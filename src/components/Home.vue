<template>
  <div class="Home">
    <h2  style=" font-size: 16px;  color:#D8D8D8; padding: 106px 0px 0px 0px;">{{ subTitle }}</h2>
    <h1 style="font-size: 48px; font-weight: bold; color:#0091FF; height:120px; padding: 20px 0px 0px 0px;">{{ title }}</h1>
        <span id="button1" style="height: 50px;width: 200px;margin-left: 650px">
        <el-radio-group v-model="radioButton" @change="change" size="medium" >
            <el-radio-button :label="true" >NEW</el-radio-button>
            <el-radio-button :label="false">TOTAL</el-radio-button>
        </el-radio-group>
        </span>
    <div style="margin: 0 auto; display: flex; flex-direction: row; justify-content: space-around;">

    <div id="main" style="width: 1200px; height: 560px;"></div>
    </div>
    <h2 style="font-size: 48px; font-weight: bold; color:#0091FF; height:120px; padding: 60px 0px 0px 0px;">{{ subTitle2 }}</h2>

    <div style="margin: 0 auto; display: flex; flex-direction: row; justify-content: space-around;padding: 0px 0px 0px 0px;">
    <div id="main2" style="width: 1200px; height: 600px;"></div>
    </div>


  </div>
</template>

<script>
import * as d3 from 'd3'
import echarts from 'echarts'

export default {
  name: 'Home',
  data () {
    return {
      msg: '这个界面是上下镜像对称的柱状图，展示每日新增和累计新增，死亡与重要政策发布节点',
      title:'WE ARE DOING BETTER...',
      subTitle: 'We compared the data of SARS 2003 with the ones of COVID-19 2020 and try to find something...',
      subTitle2:'\'CAUSE WE ARE ACTING BETTER',

      radioButton:true,
      chartOption: 0,
      tabView: 'select1',
      // markpoints
      categories: ['疫情政策'/* 0 */, '消费措施'/* 1 */, '基础设施'/* 2 */,'指导思想'/* 3 */,'教育活动'/* 4 */,'科研进展'/* 5 */,'疫情转折'/* 6 */],
      types: [
      {name: '隔离政策', color: '#7b9ce1'},
      {name: '消费措施', color: '#bd6d6c'},
      {name: '基础设施', color: '#75d874'},
      {name: 'Listeners', color: '#e0bc78'},
      {name: '其他', color: '#dc77dc'},
      {name: '科研进展', color: '#72b362'},
      {name: '疫情转折', color: '#FFD154'}
      ],  

      // 新冠数据
      // 累计治愈
      data1: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 103, 124, 171, 243, 328, 475, 632, 892, 1153, 1540, 2050, 2649, 3281, 3996, 4740, 5642, 6723, 8096, 9419, 10851, 12552, 14376, 16157, 18266, 20659, 22888, 24734, 27323, 29745, 32495, 36117, 39002, 41625, 44462, 47204, 49856, 52045, 53726, 55404, 57065, 58600, 60124, 61617, 62841, 64152, 65578, 66934, 67819, 68724, 69614, 70420, 71740, 71740, 72440, 72703, 73159, 73650, 74051, 74588, 74971, 75448, 75700, 76052, 76238, 76408, 76571, 76755, 76964, 77078, 77167, 77279, 77370, 77455, 77525, 77575, 77663, 77738, 77816, 77892, 77944, 77029, 77062, 77084, 77123, 77151, 77207, 77257, 77346, 77394, 77474, 77555, 77578, 77610, 77642, 77685, 77713, 77766, 77853, 77911, 77957, 77993, 78046, 78120, 78144, 78171, 78189, 78195, 78209, 78219, 78227, 78238, 78241, 78244, 78249, 78255, 78258, 78261, 78268, 78277, 78280, 78288, 78291, 78302, 78304, 78307, 78315, 78314, 78319, 78327, 78329, 78332, 78341, 78351, 78357, 78361, 78365, 78367, 78369, 78370, 78377, 78379, 78394, 78398, 78410, 78413, 78413, 78425, 78428, 78443, 78439, 78444, 78451],
      // 累计死亡
      data2: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 100, 100, 100, 100, 100, 213, 259, 304, 361, 425, 490, 563, 636, 722, 811, 908, 1016, 1113, 1259, 1380, 1523, 1665, 1770, 1868, 2004, 2118, 2236, 2345, 2442, 2592, 2663, 2715, 2744, 2788, 2835, 2870, 2912, 2943, 2981, 3012, 3042, 3070, 3097, 3119, 3136, 3158, 3169, 3177, 3189, 3199, 3213, 3226, 3237, 3245, 3255, 3255, 3261, 3270, 3277, 3281, 3287, 3292, 3295, 3300, 3304, 3305, 3312, 3318, 3322, 3326, 3329, 3331, 3331, 3333, 3335, 3336, 3339, 3339, 3341, 3341, 3342, 3342, 4632, 4632, 4632, 4632, 4632, 4632, 4632, 4632, 4632, 4632, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4633, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634, 4634],
      // 累计确诊
      data3:[48, 139, 139, 139, 544, 639, 901, 1377, 2076, 2857, 4630, 6095, 8149, 9092, 11791, 14380, 17205, 20438, 24324, 28018, 31161, 34546, 37198, 40171, 48315, 55220, 58761, 63851, 66492, 68500, 70548, 72436, 74185, 74576, 75891, 76288, 76741, 77150, 77658, 78064, 78497, 78824, 79251, 79824, 80026, 80151, 80270, 80409, 80552, 80651, 80695, 80735, 80761, 80790, 80796, 80817, 80824, 80851, 80880, 80881, 80894, 80928, 81008, 81008, 81054, 81093, 81171, 81218, 81285, 81340, 81394, 81439, 81470, 81518, 81554, 81589, 81620, 81639, 81669, 81708, 81740, 81802, 81865, 81907, 81953, 82052, 82160, 82249, 82295, 82341, 82692, 82719, 82735, 82747, 82758, 82788, 82798, 82804, 82816, 82827, 82830, 82836, 82858, 82862, 82874, 82875, 82877, 82880, 82881, 82883, 82885, 82886, 82887, 82901, 82918, 82919, 82926, 82929, 82933, 82941, 82947, 82954, 82960, 82965, 82967, 82971, 82971, 82974, 82985, 82992, 82993, 82995, 82995, 82999, 83001, 83017, 83021, 83021, 83022, 83027, 83030, 83036, 83040, 83043, 83046, 83057, 83064, 83075, 83132, 83181, 83221, 83265, 83293, 83325, 83352, 83378, 83396, 83418, 83430, 83449, 83462, 83483, 83500],
      // 累计确诊-累计治愈-累计死亡
      data4: [],
      // 节点
      data5:[
            {name:'武汉封城', value:[0,'1/23','4/8',76], itemStyle:{color:'#3FABFC'}},
            {name:'铁道部全国免收退票费', value:[1,'1/24','3/1',20], itemStyle:{color:'#56AAF9'}},
            {name:'火神山医院起建', value:[2,'1/24','2/3',10], itemStyle:{color:'#6BAAF6'}},
            {name:'雷神山医院起建', value:[2,'1/26','2/3',9], itemStyle:{color:'#6BAAF6'}},
            {name:'住宅小区封闭管理', value:[0,'2/14','3/20',35], itemStyle:{color:'#3FABFC'}},
            {name:'教育部宣布高考推迟', value:[4,'3/31','3/31',1], itemStyle:{color:'#8EA8F1'}},
            {name:'中央要求防范陆地边境疫情跨境输入', value:[3,'4/6','4/6',1], itemStyle:{color:'#7DA8F3'}},
            {name:'举行全国性哀悼活动', value:[4,'4/4','4/4',1], itemStyle:{color:'#8EA8F1'}},
            {name:'中央要求不允许为了追求病例零报告而瞒报漏报', value:[3,'3/30','3/30',1], itemStyle:{color:'#7DA8F3'}},
            {name:'钟南山明确表示新冠病毒人传人', value:[5,'1/20','1/20',1], itemStyle:{color:'#A1A7EE'}},
            {name:'武汉外多地确诊首例新冠病例', value:[6,'1/21','1/21',1], itemStyle:{color:'#FD8A87'}},
            {name:'武汉要求全市公共场所佩戴口罩', value:[0,'1/22','1/22',1], itemStyle:{color:'#3FABFC'}},
            {name:'首位医生因疫情殉职', value:[5,'1/23','1/23',1], itemStyle:{color:'#FE977E'}},
            {name:'浙江、广东、湖南启动重大突发公共卫生事件一级响应', value:[0,'1/23','1/23',1], itemStyle:{color:'#3FABFC'}},
            {name:'河北出现湖北省以外首例死亡病例', value:[6,'1/23','1/23',1], itemStyle:{color:'#FD8A87'}},
            {name:'湖北、重庆、山东等地决定启动重大突发公共卫生事件一级响应', value:[0,'1/23','1/23',1], itemStyle:{color:'#3FABFC'}},
            {name:'上海、广东派出第一批医疗队赶赴武汉', value:[6,'1/24','3/20',27], itemStyle:{color:'#B5A6EB'}},
            {name:'中央成立应对疫情工作领导小组', value:[3,'1/25','1/25',1], itemStyle:{color:'#7DA8F3'}},
            {name:'国家医保局免除个人负担政策扩大至疑似病人', value:[1,'1/27','1/27',20], itemStyle:{color:'#56AAF9'}},
            {name:'国家电网表示疫情防控期间居民用电客户欠费不停电', value:[1,'1/27','1/27',20], itemStyle:{color:'#56AAF9'}},
            {name:'教育部宣布国家公务员考试录用、公开遴选和公开选调面试时间推迟', value:[4,'1/28','1/28',1], itemStyle:{color:'#8EA8F1'}},
            {name:'全国累计确诊病例超过非典', value:[6,'1/28','1/28',1], itemStyle:{color:'#B5A6EB'}},
            {name:'北京小汤山医院开始重建', value:[2,'1/29','1/29',1], itemStyle:{color:'#6BAAF6'}},
            {name:'湖北组织省内防疫物资相关企业复工、复产', value:[2,'1/30','1/30',1], itemStyle:{color:'#6BAAF6'}},
            {name:'武汉建设“方舱医院”用于收治轻症患者', value:[2,'2/3','3/10',36], itemStyle:{color:'#6BAAF6'}},
            {name:'湖北纪委监委通报湖北省红十字会有关领导和干部失职失责问题', value:[3,'2/4','2/4',1], itemStyle:{color:'#FEAD6E'}},
            {name:'深圳、广州、成都市所有小区实施封闭管理', value:[3,'2/7','2/7',1], itemStyle:{color:'#7DA8F3'}},
            {name:'全国13地新增病例为0', value:[6,'2/19','2/19',1], itemStyle:{color:'#B5A6EB'}},
            {name:'教育部表示疫情未得到基本控制前不开学', value:[4,'2/28','2/28',1], itemStyle:{color:'#8EA8F1'}},
            {name:'全国全面线上教学', value:[4,'3/1','6/28',120], itemStyle:{color:'#8EA8F1'}},
            {name:'武汉产生首家“休舱”方舱医院', value:[6,'3/1','3/1',1], itemStyle:{color:'#B5A6EB'}},
            {name:'中国科研团队称新冠病毒已突变', value:[5,'3/4','3/4',1], itemStyle:{color:'#FE977E'}},
            {name:'武汉14家方舱医院11家休舱', value:[6,'3/9','3/9',1], itemStyle:{color:'#B5A6EB'}},
            {name:'4.2万援鄂医疗队员撤离', value:[6,'3/20','3/20',1], itemStyle:{color:'#B5A6EB'}},
            {name:'武汉16家方舱医院全部休舱', value:[6,'3/10','3/10',1], itemStyle:{color:'#B5A6EB'}},
            {name:'中国新冠疫苗开始人体注射实验', value:[5,'3/21','3/21',1], itemStyle:{color:'#A1A7EE'}},
            {name:'中央指导组称以武汉为主战场的全国本土疫情传播基本阻断 ', value:[6,'3/31','3/31',1], itemStyle:{color:'#B5A6EB'}},
            {name:'武汉公共交通今日全面恢复运营', value:[0,'4/22','4/22',1], itemStyle:{color:'#3FABFC'}},
            {name:'武汉在院新冠肺炎患者清零', value:[6,'4/26','4/26',1], itemStyle:{color:'#B5A6EB'}},
            {name:'北京小汤山医院清零', value:[6,'4/28','4/28',1], itemStyle:{color:'#B5A6EB'}},
            {name:'2019年12月27日确诊第一例', value:[6,'1/17','1/17',1], itemStyle:{color:'#FD8A87'}},
      ],

      // SARS数据
      // 新增确诊 从1.17开始 初期数据未公开
      dataS1:[100, 6, 6, 6, 6, 6, 6, 6, 6, 6, 6, 6, 6, 6, 6, 6, 6, 6, 6, 10 /* 由于信息未公开 118分摊到前面的每日*/, 45/* 2.6 */, 14, 14, 14, 25, 25/* 2.11 */, 25, 25, 25, 25, 25, 25, 25, 25, 25, 25, 25, 25, 25, 25, 25, 25, 34/* 2.28 */, 6, 6, 6, 7/* 3.4 25*/, 3, 3, 4/* 3.7 11*/, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 13, 23/* 4.10 465*/, 70, 70, 70, 70, 70, 70, 70, 70, 70, 81 /* 4.20 711*/, 194, 159, 147, 125, 180, 154, 161, 203, 202, 166, 187, 176, 181, 163, 160, 138, 159, 146, 118, 85, 69, 75, 80, 55, 52, 39, 28, 28, 12, 17, 12, 26, 20, 34, 16, 8, 9],
      // 累计数据
      dataS2: [],
      // 节点
      dataS3:[
            {name:'基本确定致病源为冠状病毒,但结论未上报', value:[5,'2/7','2/28',12], itemStyle:{color:'#FE977E'}},
            {name:'广东省要求各卫生部门认真调查', value:[3,'1/23','1/23',1], itemStyle:{color:'#B4E0AE'}},
            {name:'板蓝根被疯抢', value:[2,'2/9','2/9',1], itemStyle:{color:'#FEB666'}},
            {name:'广东省声称所有病人的病情均在控制中', value:[3,'2/11','2/11',1], itemStyle:{color:'#FEAD6E'}},
            {name:'疾控中心负责人认为全国近期内不会发生大范围流行', value:[0,'2/12','2/12',1], itemStyle:{color:'#FEB666'}},
            {name:'春运开始', value:[4,'3/31','3/31',1], itemStyle:{color:'#FEA177'}},
            {name:'中国足球队与巴西足球队进行比赛 现场超五万人观赛', value:[4,'2/12','2/12',1], itemStyle:{color:'#FEA177'}},
            {name:'广州罗大佑演唱会照常进行', value:[4,'2/18','2/18',1], itemStyle:{color:'#FEA177'}},
            {name:'广东声称连续五天无新病例出现', value:[6,'2/9','2/14',1], itemStyle:{color:'#FD8A87'}},
            {name:'染病教授赴港 传染七名游客后离世', value:[6,'2/21','3/4',11], itemStyle:{color:'#FD8A87'}},
            {name:'北京报道第一例输入性非典病例', value:[6,'3/6','3/6',1], itemStyle:{color:'#FD8A87'}},
            {name:'因无防护措施，北京医院11名医护人员感染，2名离世', value:[6,'3/15','3/20',6], itemStyle:{color:'#FD8A87'}},
            {name:'中国推出了《非典型肺炎防治技术方案》', value:[5,'3/31','3/31',1], itemStyle:{color:'#E1E29F'}},
            {name:'中国政府向WHO确认将申报所有案例', value:[0,'4/2','4/2',1], itemStyle:{color:'#72DFC6'}},
            {name:'卫生部部长表示在北京工作、旅游十分安全', value:[0,'4/3','4/3',1], itemStyle:{color:'#FEA177'}},
            {name:'为显示已经有效控制了非典型肺炎疫情，开展广州市春季健身万人长跑活动', value:[4,'4/6','4/6',1], itemStyle:{color:'#FEA177'}},
            {name:'北京疫情持续爆发', value:[6,'4/17','4/20',4], itemStyle:{color:'#FDE394'}},
            {name:'教育部宣布全国硕士研究生复试时间推迟', value:[4,'4/18','4/18',1], itemStyle:{color:'#CCE2A7'}},
            {name:'北京高校学生五一期间不离校回家', value:[0,'5/1','5/5',5], itemStyle:{color:'#72DFC6'}},
            {name:'中央宣布撤销北京市市长孟学农和卫生部部长张文康的党内职务', value:[1,'4/20','4/20',20], itemStyle:{color:'#92E0BB'}},
            {name:'北京多所高校宣布停课', value:[4,'4/20','4/20',1], itemStyle:{color:'#CCE2A7'}},
            {name:'卫生部宣布实行“疫情一日一报制”', value:[0,'4/20','4/20',1], itemStyle:{color:'#72DFC6'}},
            {name:'北京小汤山医院启用', value:[2,'4/30','4/30',1], itemStyle:{color:'#A1E0B6'}},
            {name:'北京市中小学停课', value:[4,'4/24','5/8',14], itemStyle:{color:'#CCE2A7'}},
            {name:'铁道部全国免收退票费', value:[1,'4/24','4/24',36], itemStyle:{color:'#92E0BB'}},
            {name:'卫生部要求非典型肺炎防治场所严禁使用中央空调', value:[0,'4/30','4/30',1], itemStyle:{color:'#72DFC6'}},
            {name:'“钟南山谈非典防治”科教片将向全国公开发行', value:[0,'5/3','5/3',1], itemStyle:{color:'#72DFC6'}},
            {name:'北京中日友好医院作为非典定点医院投入使用', value:[2,'5/8','5/8',1], itemStyle:{color:'#A1E0B6'}},
            {name:'北京非典新增病例数降至个位', value:[6,'5/17','5/17',1], itemStyle:{color:'#FDE394'}},
            {name:'2002年12月25日确诊第一例', value:[6,'1/17','1/17',1], itemStyle:{color:'#FD8A87'}},
           
      ],

    }
  },
  mounted () {
    let _this = this
     _this.draw()
  },
  created () {
    for(let i=0;i<this.data1.length;i++){
        this.data4[i] = this.data3[i] - this.data2[i] - this.data1[i]
      }
      this.dataS2[0]=this.dataS1[0]
      for (let i=1;i<this.dataS1.length;i++){
        this.dataS2[i] = this.dataS1[i]+this.dataS2[i-1]
      }
  },
  methods: {
    draw () {
      this.optionNew = {
          title: {
              textStyle: {
                color: '#BCBBB9',
                fontWeight: 'bolder',
                fontSize: 37
            },
              text: 'Daily Cases',
              left: 10,
              padding:[10,0,0,60],
          },
          grid: {
            top:40,
            containLabel: false,
          },
          toolbox: {
              feature: {
                  dataZoom: {
                      yAxisIndex: false,
                  },
                  saveAsImage: {
                      pixelRatio: 2
                  }
              }
          },
          tooltip: {
              trigger: 'axis',
              axisPointer: {
                  type: 'shadow'
              }
          },
          grid: [
              {bottom: '55%'},
              {top:'45%'}
              // containLabel:true,
          ],
          dataZoom: [{
              type: 'inside',
              xAxisIndex: [0,1],
          }, {
              type: 'slider'
          }],
          xAxis: [
              {type: 'category',
              data: ['1/17', '1/18', '1/19', '1/20', '1/21', '1/22', '1/23', '1/24', '1/25', '1/26', '1/27', '1/28', '1/29', '1/30', '1/31', '2/1', '2/2', '2/3', '2/4', '2/5', '2/6', '2/7', '2/8', '2/9', '2/10', '2/11', '2/12', '2/13', '2/14', '2/15', '2/16', '2/17', '2/18', '2/19', '2/20', '2/21', '2/22', '2/23', '2/24', '2/25', '2/26', '2/27', '2/28', '2/29', '3/1', '3/2', '3/3', '3/4', '3/5', '3/6', '3/7', '3/8', '3/9', '3/10', '3/11', '3/12', '3/13', '3/14', '3/15', '3/16', '3/17', '3/18', '3/19', '3/20', '3/21', '3/22', '3/23', '3/24', '3/25', '3/26', '3/27', '3/28', '3/29', '3/30', '3/31', '4/1', '4/2', '4/3', '4/4', '4/5', '4/6', '4/7', '4/8', '4/9', '4/10', '4/11', '4/12', '4/13', '4/14', '4/15', '4/16', '4/17', '4/18', '4/19', '4/20', '4/21', '4/22', '4/23', '4/24', '4/25', '4/26', '4/27', '4/28', '4/29', '4/30', '5/1', '5/2', '5/3', '5/4', '5/5', '5/6', '5/7', '5/8', '5/9', '5/10', '5/11', '5/12', '5/13', '5/14', '5/15', '5/16', '5/17', '5/18', '5/19', '5/20', '5/21', '5/22', '5/23', '5/24', '5/25', '5/26', '5/27', /* '5/28', '5/29', '5/30', '5/31', '6/1', '6/2', '6/3', '6/4', '6/5', '6/6', '6/7', '6/8', '6/9', '6/10', '6/11', '6/12', '6/13', '6/14', '6/15', '6/16', '6/17', '6/18', '6/19', '6/20', '6/21', '6/22', '6/23', '6/24', '6/25', '6/26', '6/27' */],
              silent: false,
              splitLine: {
                  show: false
              },
              splitArea: {
                  show: false
              },
              gridIndex: 0,
              z:3
              },

              {type: 'category',
              data: ['1/17', '1/18', '1/19', '1/20', '1/21', '1/22', '1/23', '1/24', '1/25', '1/26', '1/27', '1/28', '1/29', '1/30', '1/31', '2/1', '2/2', '2/3', '2/4', '2/5', '2/6', '2/7', '2/8', '2/9', '2/10', '2/11', '2/12', '2/13', '2/14', '2/15', '2/16', '2/17', '2/18', '2/19', '2/20', '2/21', '2/22', '2/23', '2/24', '2/25', '2/26', '2/27', '2/28', '2/29', '3/1', '3/2', '3/3', '3/4', '3/5', '3/6', '3/7', '3/8', '3/9', '3/10', '3/11', '3/12', '3/13', '3/14', '3/15', '3/16', '3/17', '3/18', '3/19', '3/20', '3/21', '3/22', '3/23', '3/24', '3/25', '3/26', '3/27', '3/28', '3/29', '3/30', '3/31', '4/1', '4/2', '4/3', '4/4', '4/5', '4/6', '4/7', '4/8', '4/9', '4/10', '4/11', '4/12', '4/13', '4/14', '4/15', '4/16', '4/17', '4/18', '4/19', '4/20', '4/21', '4/22', '4/23', '4/24', '4/25', '4/26', '4/27', '4/28', '4/29', '4/30', '5/1', '5/2', '5/3', '5/4', '5/5', '5/6', '5/7', '5/8', '5/9', '5/10', '5/11', '5/12', '5/13', '5/14', '5/15', '5/16', '5/17', '5/18', '5/19', '5/20', '5/21', '5/22', '5/23', '5/24', '5/25', '5/26', '5/27', /* '5/28', '5/29', '5/30', '5/31', '6/1', '6/2', '6/3', '6/4', '6/5', '6/6', '6/7', '6/8', '6/9', '6/10', '6/11', '6/12', '6/13', '6/14', '6/15', '6/16', '6/17', '6/18', '6/19', '6/20', '6/21', '6/22', '6/23', '6/24', '6/25', '6/26', '6/27' */],
              silent: false,
              show:false,
              splitLine: {
                  show: false
              },
              splitArea: {
                  show: false
              },
              gridIndex: 1,
              },

          ],
          yAxis: [
            {
              // 新增人数
              type: 'value',
              min: 0,
              max: 9000,
              gridIndex: 0,
          },
           {
              // 新增人数
              type: 'value',
              inverse: true,
              min: 0,
              max: 300,
              gridIndex: 1,
          },
          ],
          series: [
            // 新冠
            {              
              name: 'COVID-19新增确诊',
              type: 'bar',
              barGap:'0%',
              barCategoryGap:'0%',
              xAxisIndex: 0,
              yAxisIndex: 0,
              data: [4, 0, 0, 91, 0, 0, 405, 95, 262, 476, 699, 781, 1773, 1465, 2054, 1543, 2099, 2589, 2825, 3233, 3886, 3694, 3143, 3385, 2652, 2973, 8144, 6905, 3541, 5090, 2641, 2008, 2048, 1888, 1749, 391, 1315, 397, 453, 409, 508, 406, 433, 327, 427, 573, 202, 125, 119, 139, 143, 99, 44, 40, 26, 29, 6, 21, 7, 27, 29, 1, 13, 34, 80, 0, 46, 39, 78, 47, 67, 55, 54, 45, 31, 48, 36, 35, 31, 19, 30, 39, 32, 62, 63, 42, 46, 99, 108, 89, 46, 46, 351, 27, 16, 12, 11, 30, 10, 6, 12, 11, 3, 6, 22, 4, 12, 1, 2, 3, 1, 2, 2, 1, 1, 14, 17, 1, 7, 3, 4, 8, 6, 7, 6, 5, 2, 4, 0, 3, 11, 7, 1, 2, 0, 4, 2, 16, 4, 0, 1, 5, 3, 6, 4, 3, 3, 11, 7, 11, 57, 49, 40, 44, 28, 32, 27, 26, 18, 22, 12, 19, 13, 21, 17],
              itemStyle: {
                color: new this.$echarts.graphic.LinearGradient(
                    0, 0, 0, 1,
                    [
                        {offset: 0, color: 'rgba(0,145,255,0.2)'},
                        {offset: 1, color: 'rgba(0,145,255,0.8)'}
                    ]
                )
            },
              // Set `large` for large data amount
              large: true,
              z:0,
          },
          /*
            {
              name: 'COVID-19累计确诊',
              type: 'line', 
              xAxisIndex: 0,
              yAxisIndex: 0,
              data: this.data3,
              // Set `large` for large data amount
              smooth: true,
              large: true,
              color: '#0091FF',
              z:1,
              itemStyle: {
                opacity:0
            },
            lineStyle: {
                opacity:0
            },
            
              markPoint: {
                data: [
                    {name: '武汉封城', value: '武汉封城', xAxis: '1/23', yAxis: 6095}, 
                    {name: '火神山医院交付', value: '火神山医院交付', xAxis: '2/2', yAxis: 17205},
                    {name: '总死亡人数超过非典', value: '总死亡人数超过非典', xAxis: '2/8', yAxis: 37198},
                    {name: '雷神山医院交付使用', value: '雷神山医院交付使用', xAxis: '2/8', yAxis: 6095},
                    {name: '李文亮等被追授全国疫情防控先进个人称号', value: '李文亮等被追授全国疫情防控先进个人称号', xAxis: '3/5', yAxis: 6095},
                    {name: '习近平抵达武汉考察新冠肺炎疫情防控工作', value: '习近平抵达武汉考察新冠肺炎疫情防控工作', xAxis: '3/10', yAxis: 6095},
                    {name: '武汉16家方舱医院全部休舱', value: '武汉16家方舱医院全部休舱', xAxis: '3/10', yAxis: 6095},
                    {name: '健康码开始发放', value: '健康码开始发放', xAxis: '3/10', yAxis: 6095},
                    {name: '外防输入成为重中之重', value: '外防输入成为重中之重', xAxis: '3/16', yAxis: 6095},
                    {name: '安徽解除封闭管理', value: '安徽解除封闭管理', xAxis: '3/18', yAxis: 6095},
                    {name: '高考宣布延期', value: '高考宣布延期', xAxis: '3/31', yAxis: 6095},
                    {name: '全国哀悼', value: '全国哀悼', xAxis: '4/4', yAxis: 6095},
                ]
            }, 
          }, */
          // SARS
          /*
          {              
              name: 'SARS累计患病',
              type: 'line',
              xAxisIndex: 1,
              yAxisIndex: 1,
              data: this.dataS2,
              itemStyle: {
                opacity:0
            },
            lineStyle: {
                opacity:0
            },
              // Set `large` for large data amount
              large: true,
          },*/
            {              
              name: 'SARS新增确诊',
              type: 'bar',
              barGap:'0%',
              barCategoryGap:'0%',
              data: this.dataS1,
              itemStyle: {
                color: new this.$echarts.graphic.LinearGradient(
                    0, 0, 0, 1,
                    [
                        {offset: 0, color: 'rgba(86,215,182,0.8)'},
                        {offset: 1, color: 'rgba(86,215,182,0.2)'}
                    ]
                )
            },
              // Set `large` for large data amount
              large: true,
              xAxisIndex: 1,
              yAxisIndex: 1,
              z:2,
          },
          ]
      },
      this.optionTotal = {
          title: {
              textStyle: {
                color: '#BCBBB9',
                fontWeight: 'bolder',
                fontSize: 37
            },
              text: 'Daily Cases',
              left: 10,
              padding:[10,0,0,60],
          },
          grid: {
            top:40,
            containLabel: false,
          },
          toolbox: {
              feature: {
                  dataZoom: {
                      yAxisIndex: false,
                  },
                  saveAsImage: {
                      pixelRatio: 2
                  }
              }
          },
          tooltip: {
              trigger: 'axis',
              axisPointer: {
                  type: 'shadow'
              }
          },
          grid: [
              {bottom: '55%'},
              {top:'45%'}
              // containLabel:true,
          ],
          dataZoom: [{
              type: 'inside',
              xAxisIndex: [0,1],
          }, {
              type: 'slider'
          }],
          xAxis: [
              {type: 'category',
              data: ['1/17', '1/18', '1/19', '1/20', '1/21', '1/22', '1/23', '1/24', '1/25', '1/26', '1/27', '1/28', '1/29', '1/30', '1/31', '2/1', '2/2', '2/3', '2/4', '2/5', '2/6', '2/7', '2/8', '2/9', '2/10', '2/11', '2/12', '2/13', '2/14', '2/15', '2/16', '2/17', '2/18', '2/19', '2/20', '2/21', '2/22', '2/23', '2/24', '2/25', '2/26', '2/27', '2/28', '2/29', '3/1', '3/2', '3/3', '3/4', '3/5', '3/6', '3/7', '3/8', '3/9', '3/10', '3/11', '3/12', '3/13', '3/14', '3/15', '3/16', '3/17', '3/18', '3/19', '3/20', '3/21', '3/22', '3/23', '3/24', '3/25', '3/26', '3/27', '3/28', '3/29', '3/30', '3/31', '4/1', '4/2', '4/3', '4/4', '4/5', '4/6', '4/7', '4/8', '4/9', '4/10', '4/11', '4/12', '4/13', '4/14', '4/15', '4/16', '4/17', '4/18', '4/19', '4/20', '4/21', '4/22', '4/23', '4/24', '4/25', '4/26', '4/27', '4/28', '4/29', '4/30', '5/1', '5/2', '5/3', '5/4', '5/5', '5/6', '5/7', '5/8', '5/9', '5/10', '5/11', '5/12', '5/13', '5/14', '5/15', '5/16', '5/17', '5/18', '5/19', '5/20', '5/21', '5/22', '5/23', '5/24', '5/25', '5/26', '5/27', /* '5/28', '5/29', '5/30', '5/31', '6/1', '6/2', '6/3', '6/4', '6/5', '6/6', '6/7', '6/8', '6/9', '6/10', '6/11', '6/12', '6/13', '6/14', '6/15', '6/16', '6/17', '6/18', '6/19', '6/20', '6/21', '6/22', '6/23', '6/24', '6/25', '6/26', '6/27' */],
              silent: false,
              splitLine: {
                  show: false
              },
              splitArea: {
                  show: false
              },
              gridIndex: 0,
              z:3
              },

              {type: 'category',
              data: ['1/17', '1/18', '1/19', '1/20', '1/21', '1/22', '1/23', '1/24', '1/25', '1/26', '1/27', '1/28', '1/29', '1/30', '1/31', '2/1', '2/2', '2/3', '2/4', '2/5', '2/6', '2/7', '2/8', '2/9', '2/10', '2/11', '2/12', '2/13', '2/14', '2/15', '2/16', '2/17', '2/18', '2/19', '2/20', '2/21', '2/22', '2/23', '2/24', '2/25', '2/26', '2/27', '2/28', '2/29', '3/1', '3/2', '3/3', '3/4', '3/5', '3/6', '3/7', '3/8', '3/9', '3/10', '3/11', '3/12', '3/13', '3/14', '3/15', '3/16', '3/17', '3/18', '3/19', '3/20', '3/21', '3/22', '3/23', '3/24', '3/25', '3/26', '3/27', '3/28', '3/29', '3/30', '3/31', '4/1', '4/2', '4/3', '4/4', '4/5', '4/6', '4/7', '4/8', '4/9', '4/10', '4/11', '4/12', '4/13', '4/14', '4/15', '4/16', '4/17', '4/18', '4/19', '4/20', '4/21', '4/22', '4/23', '4/24', '4/25', '4/26', '4/27', '4/28', '4/29', '4/30', '5/1', '5/2', '5/3', '5/4', '5/5', '5/6', '5/7', '5/8', '5/9', '5/10', '5/11', '5/12', '5/13', '5/14', '5/15', '5/16', '5/17', '5/18', '5/19', '5/20', '5/21', '5/22', '5/23', '5/24', '5/25', '5/26', '5/27', /* '5/28', '5/29', '5/30', '5/31', '6/1', '6/2', '6/3', '6/4', '6/5', '6/6', '6/7', '6/8', '6/9', '6/10', '6/11', '6/12', '6/13', '6/14', '6/15', '6/16', '6/17', '6/18', '6/19', '6/20', '6/21', '6/22', '6/23', '6/24', '6/25', '6/26', '6/27' */],
              silent: false,
              show:false,
              splitLine: {
                  show: false
              },
              splitArea: {
                  show: false
              },
              gridIndex: 1,
              },

          ],
          yAxis: [
            {
              // 累计人数
              type: 'value',
              min: 0,
              max: 100000,
              gridIndex: 0,
          },
           {
              // 累计人数
              type: 'value',
              inverse: true,
              min: 0,
              max: 6000,
              gridIndex: 1,
          },
          ],
          series: [
            // 新冠
            {              
              name: 'COVID-19累计确诊',
              type: 'bar',
              barGap:'0%',
              barCategoryGap:'0%',
              xAxisIndex: 0,
              yAxisIndex: 0,
              stack:'1',
              data: this.data3,
              itemStyle: {
                color: new this.$echarts.graphic.LinearGradient(
                    0, 0, 0, 1,
                    [
                        {offset: 0, color: 'rgba(0,145,255,0.2)'},
                        {offset: 1, color: 'rgba(0,145,255,0.8)'}
                    ]
                )
            },
              // Set `large` for large data amount
              large: true,
          },
          /* {              
              name: 'COVID-19累计康复',
              type: 'bar',
              barGap:'0%',
              barCategoryGap:'0%',
              xAxisIndex: 0,
              yAxisIndex: 0,
              stack:'1',
              data: this.data1,
              color: '#E9E6DF', 
              // Set `large` for large data amount
              large: true,
          },
          {              
              name: 'COVID-19累计死亡',
              type: 'bar',
              barGap:'0%',
              barCategoryGap:'0%',
              xAxisIndex: 0,
              yAxisIndex: 0,
              stack:'1',
              data: this.data2,
              color: 'rgba(255, 0, 0, 0.45)', 
              // Set `large` for large data amount
              large: true,
          }, */
          // SARS
          {              
              name: 'SARS累计患病',
              type: 'bar',
              barGap:'0%',
              barCategoryGap:'0%',
              xAxisIndex: 1,
              yAxisIndex: 1,
              itemStyle: {
                color: new this.$echarts.graphic.LinearGradient(
                    0, 0, 0, 1,
                    [
                        {offset: 0, color: 'rgba(86,215,182,0.8)'},
                        {offset: 1, color: 'rgba(86,215,182,0.2)'}
                    ]
                )
            },
              data: this.dataS2,
              // Set `large` for large data amount
              large: true,
          },
          ]
      },
        this.chart = this.$echarts.init(document.getElementById('main'))
        console.log(this.radioButton)
        this.chart.setOption(this.optionNew)
      this.optionMarkPoints = {
                title: [{
            text: 'Covid-19 Policy',
            padding:[0,0,0,60],
            textStyle: {
                color: '#BCBBB9',
                fontWeight: 'bolder',
                fontSize:32,
            },
            subtext: '数据来自卫健委',
            subtextStyle: {
                color: '#BCBBB9',
                fontSize:12,
            },

        },
        {
          text: 'SARS Policy',
          padding:[500,0,0,60],
            textStyle: {
                color: '#BCBBB9',
                fontWeight: 'bolder',
                fontSize:32,
            },
            subtext: '数据来自卫健委',
            subtextStyle: {
                color: '#BCBBB9',
                fontSize:12,
            },
        }
        ],
        
          tooltip:{
             formatter: function (params) {
                return params.marker + params.name + ': ' + params.value[3] + '天';
            }
          },
          dataZoom: [{
                type: 'slider',
                // filterMode: 'weakFilter',
                showDataShadow: false,
                xAxisIndex: [0,1],
                top: 560,
                height: 10,
                borderColor: 'transparent',
                backgroundColor: '#e2e2e2',
                handleIcon: 'M10.7,11.9H9.3c-4.9,0.3-8.8,4.4-8.8,9.4c0,5,3.9,9.1,8.8,9.4h1.3c4.9-0.3,8.8-4.4,8.8-9.4C19.5,16.3,15.6,12.2,10.7,11.9z M13.3,24.4H6.7v-1.2h6.6z M13.3,22H6.7v-1.2h6.6z M13.3,19.6H6.7v-1.2h6.6z', // jshint ignore:line
                handleSize: 20,
                handleStyle: {
                    shadowBlur: 6,
                    shadowOffsetX: 1,
                    shadowOffsetY: 2,
                    shadowColor: '#aaa'
                },
                labelFormatter: ''
            }, {
                type: 'inside',
                filterMode: 'weakFilter'
            },
            {
              type: 'slider',
               show:false,
            }],
            grid: [{
                height: 200
            },
            {
              top:280,
              height:200
            }
            ],
            legend:{

            },
            xAxis: [{
                type:'category',
                nameTextStyle:{
                  color:'#F1EBE9'
                },
                axisTick:{
                  lineStyle:{
                    color:'#F1EBE9'
                  }
                },
                gridIndex: 0,
                data: ['1/17', '1/18', '1/19', '1/20', '1/21', '1/22', '1/23', '1/24', '1/25', '1/26', '1/27', '1/28', '1/29', '1/30', '1/31', '2/1', '2/2', '2/3', '2/4', '2/5', '2/6', '2/7', '2/8', '2/9', '2/10', '2/11', '2/12', '2/13', '2/14', '2/15', '2/16', '2/17', '2/18', '2/19', '2/20', '2/21', '2/22', '2/23', '2/24', '2/25', '2/26', '2/27', '2/28', '2/29', '3/1', '3/2', '3/3', '3/4', '3/5', '3/6', '3/7', '3/8', '3/9', '3/10', '3/11', '3/12', '3/13', '3/14', '3/15', '3/16', '3/17', '3/18', '3/19', '3/20', '3/21', '3/22', '3/23', '3/24', '3/25', '3/26', '3/27', '3/28', '3/29', '3/30', '3/31', '4/1', '4/2', '4/3', '4/4', '4/5', '4/6', '4/7', '4/8', '4/9', '4/10', '4/11', '4/12', '4/13', '4/14', '4/15', '4/16', '4/17', '4/18', '4/19', '4/20', '4/21', '4/22', '4/23', '4/24', '4/25', '4/26', '4/27', '4/28', '4/29', '4/30', '5/1', '5/2', '5/3', '5/4', '5/5', '5/6', '5/7', '5/8', '5/9', '5/10', '5/11', '5/12', '5/13', '5/14', '5/15', '5/16', '5/17', '5/18', '5/19', '5/20', '5/21', '5/22', '5/23', '5/24', '5/25', '5/26', '5/27', /* '5/28', '5/29', '5/30', '5/31', '6/1', '6/2', '6/3', '6/4', '6/5', '6/6', '6/7', '6/8', '6/9', '6/10', '6/11', '6/12', '6/13', '6/14', '6/15', '6/16', '6/17', '6/18', '6/19', '6/20', '6/21', '6/22', '6/23', '6/24', '6/25', '6/26', '6/27' */],
            },{
                type:'category',
                nameTextStyle:{
                  color:'#F1EBE9'
                },

                gridIndex: 1,
                show:false,
                data: ['1/17', '1/18', '1/19', '1/20', '1/21', '1/22', '1/23', '1/24', '1/25', '1/26', '1/27', '1/28', '1/29', '1/30', '1/31', '2/1', '2/2', '2/3', '2/4', '2/5', '2/6', '2/7', '2/8', '2/9', '2/10', '2/11', '2/12', '2/13', '2/14', '2/15', '2/16', '2/17', '2/18', '2/19', '2/20', '2/21', '2/22', '2/23', '2/24', '2/25', '2/26', '2/27', '2/28', '2/29', '3/1', '3/2', '3/3', '3/4', '3/5', '3/6', '3/7', '3/8', '3/9', '3/10', '3/11', '3/12', '3/13', '3/14', '3/15', '3/16', '3/17', '3/18', '3/19', '3/20', '3/21', '3/22', '3/23', '3/24', '3/25', '3/26', '3/27', '3/28', '3/29', '3/30', '3/31', '4/1', '4/2', '4/3', '4/4', '4/5', '4/6', '4/7', '4/8', '4/9', '4/10', '4/11', '4/12', '4/13', '4/14', '4/15', '4/16', '4/17', '4/18', '4/19', '4/20', '4/21', '4/22', '4/23', '4/24', '4/25', '4/26', '4/27', '4/28', '4/29', '4/30', '5/1', '5/2', '5/3', '5/4', '5/5', '5/6', '5/7', '5/8', '5/9', '5/10', '5/11', '5/12', '5/13', '5/14', '5/15', '5/16', '5/17', '5/18', '5/19', '5/20', '5/21', '5/22', '5/23', '5/24', '5/25', '5/26', '5/27', /* '5/28', '5/29', '5/30', '5/31', '6/1', '6/2', '6/3', '6/4', '6/5', '6/6', '6/7', '6/8', '6/9', '6/10', '6/11', '6/12', '6/13', '6/14', '6/15', '6/16', '6/17', '6/18', '6/19', '6/20', '6/21', '6/22', '6/23', '6/24', '6/25', '6/26', '6/27' */],
            }],
            yAxis: [{
                data: this.categories,
                nameTextStyle:{
                  color:'#F1EBE9'
                },
                axisTick:{
                  lineStyle:{
                    color:'#F1EBE9'
                  }
                },

                gridIndex: 0,
            },
            {
              data: this.categories,
                nameTextStyle:{
                  color:'#F1EBE9'
                },
                axisTick:{
                  lineStyle:{
                    color:'#F1EBE9'
                  }
                },
              inverse: true,
              gridIndex: 1,
            }],
            series: [{
                type: 'custom',
                yAxisIndex: 0,
                xAxisIndex: 0,
                renderItem:  function(params, api){
                        console.log('renderisCalled');
                        var categoryIndex = api.value(0);
                        var start = api.coord([api.value(1), categoryIndex]);
                        var end = api.coord([api.value(2), categoryIndex]);
                        var height = api.size([0, 1])[1] * 0.6;

                        return {
                            type: 'group',
                            children : [
                            {
                            type:'rect',
                            shape: {
                                x: start[0],
                                y: start[1] - height / 3,
                                width: end[0] - start[0],
                                height: height/1.5
                            },
                            style: api.style()
                                },
                            {
                            type: 'circle',
                            shape: {
                                cx: start[0],
                                cy: start[1],
                                r:  height/3,
                            },
                            style: api.style()
                            },
                            {
                            type: 'circle',
                            shape: {
                                cx: end[0],
                                cy: start[1],
                                r:  height/3,
                            },
                            style: api.style()
                            },
                            ]
                        }

                },
                itemStyle: {
                    opacity: 1,
                },
                clip:true,
                data: this.data5,
                encode: {
                    x: [1, 2],
                    y: 0
                },
            },
            {
                type: 'custom',
                yAxisIndex: 1,
                xAxisIndex: 1,
                renderItem:  function(params, api){
                        console.log('renderisCalled');
                        var categoryIndex = api.value(0);
                        var start = api.coord([api.value(1), categoryIndex]);
                        var end = api.coord([api.value(2), categoryIndex]);
                        var height = api.size([0, 1])[1] * 0.6;

                        return {
                            type: 'group',
                            children : [
                            {
                            type:'rect',
                            shape: {
                                x: start[0],
                                y: start[1] - height / 3,
                                width: end[0] - start[0],
                                height: height/1.5
                            },
                            style: api.style()
                                },
                            {
                            type: 'circle',
                            shape: {
                                cx: start[0],
                                cy: start[1],
                                r:  height/3,
                            },
                            style: api.style()
                            },
                            {
                            type: 'circle',
                            shape: {
                                cx: end[0],
                                cy: start[1],
                                r:  height/3,
                            },
                            style: api.style()
                            },
                            ]
                        }

                },
                itemStyle: {
                    opacity: 1,
                },
                clip:true,
                data: this.dataS3,
                encode: {
                    x: [1, 2],
                    y: 0
                },
            }]
      },
      this.chart2 = this.$echarts.init(document.getElementById('main2'))
      this.chart2.setOption(this.optionMarkPoints)
    },
    change:function() {
      console.log("change is called")
        if(this.radioButton === true)
        this.chart.setOption(this.optionNew)
        else {
        this.chart.setOption(this.optionTotal)
        }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 0px;
}

a {
  color: #42b983;
}
</style>
