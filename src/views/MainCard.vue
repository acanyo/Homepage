<template>
  <div class="mainCard">
    <div class="header">
      <div class="avatar" :emjoi="config.emjoi">
        <img :src="config.avatarUrl" alt="" />
      </div>

      <div class="sayHi">
        <h1>
          Hi, I'm
          <span class="name" :data-text="config.name">
            {{ config.name }}
          </span>
        </h1>

        <div class="contactTags">
          <a v-if="config.contactInfo.qq" :href="'https://wpa.qq.com/msgrd?v=3&uin=' + config.contactInfo.qq + '&site=qq&menu=yes'" target="_blank" class="tag hover contact-tag">
            <Icon icon="simple-icons:tencentqq" width="16" height="16" />
            QQ
          </a>
          <a v-if="config.contactInfo.github" :href="config.contactInfo.github" target="_blank" class="tag hover contact-tag">
            <Icon icon="mdi:github" width="16" height="16" />
            GitHub
          </a>
          <a v-if="config.contactInfo.email" :href="'mailto:' + config.contactInfo.email" class="tag hover contact-tag">
            <Icon icon="mdi:email" width="16" height="16" />
            邮箱
          </a>
          <a v-if="config.contactInfo.blog" :href="config.contactInfo.blog" target="_blank" class="tag hover contact-tag">
            <Icon icon="fa7-solid:blog" width="16" height="16" />
            博客
          </a>
        </div>
      </div>
    </div>

    <div class="content">
      <div class="leftBox">
        <!-- 欢迎信息 -->
        <div class="card welcome-card">
          <div class="welcome-content">
            <div id="welcome-info" class="welcome-info">
              <div class="loading-welcome">{{ welcomeConfig.welcomeEmoji }} 正在获取位置信息...</div>
            </div>
          </div>
        </div>

        <!-- 人格类型 -->
        <div class="card personality-card">
          <div class="personality-content">
            <div class="personality-info">
              <div class="personality-type">{{ welcomeConfig.personality.type }}</div>
              <div class="personality-name">{{ welcomeConfig.personality.name }}</div>
            </div>
            <p class="personality-description">{{ welcomeConfig.personality.description }}</p>
            
            <!-- 性格维度进度条 -->
            <div class="personality-dimensions">
              <div 
                v-for="(dimension, index) in welcomeConfig.personality.dimensions" 
                :key="index"
                class="dimension-bar"
              >
                <div class="dimension-labels">
                  <span class="dimension-left">{{ dimension.left }} {{ dimension.leftPercent }}%</span>
                  <span class="dimension-right">{{ dimension.rightPercent }}% {{ dimension.right }}</span>
                </div>
                <div class="progress-container">
                  <div 
                    class="progress-left" 
                    :style="{ 
                      width: dimension.leftPercent + '%',
                      backgroundColor: dimension.leftColor,
                      minWidth: '2px'
                    }"
                  ></div>
                  <div 
                    class="progress-right" 
                    :style="{ 
                      width: dimension.rightPercent + '%',
                      backgroundColor: dimension.rightColor,
                      minWidth: '2px'
                    }"
                  ></div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- 时间显示 -->
        <div class="card">
          <div class="time-progress">
            <h3>时光⌛</h3>
            <div class="progress-item">
              <p>☀️今天已经过去了 {{ hoursPassed }} / 24 小时</p>
              <div class="progress-bar">
                <div
                  class="progress-fill"
                  :style="{ width: hoursProgress + '%' }"
                ></div>
              </div>
            </div>

            <div class="progress-item">
              <p>📆本周已经过去了 {{ daysInWeekPassed }} / 7 天</p>
              <div class="progress-bar">
                <div
                  class="progress-fill"
                  :style="{ width: weekProgress + '%' }"
                ></div>
              </div>
            </div>

            <div class="progress-item">
              <p>
                🌙本月已经过去了 {{ daysInMonthPassed }} /
                {{ daysInCurrentMonth }} 天
              </p>
              <div class="progress-bar">
                <div
                  class="progress-fill"
                  :style="{ width: monthProgress + '%' }"
                ></div>
              </div>
            </div>

            <div class="progress-item">
              <p>
                ⭐今年已经过去了 {{ daysInYearPassed }} /
                {{ daysInCurrentYear }} 天
              </p>
              <div class="progress-bar">
                <div
                  class="progress-fill"
                  :style="{ width: yearProgress + '%' }"
                ></div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="rightBox">
        <div class="card">
          <p>你好鸭，很高兴认识你👋</p>
          <p>
            我叫
            <b>{{ config.name }}</b>
            <b class="zodiac">{{ config.zodiac }}</b>
          </p>
          <p>
            是一名
            <span v-for="(i, index) in config.professions" :key="index">
              <b>{{ i }}</b>
              <span v-if="index < config.professions.length - 1">、</span>
            </span>
          </p>

          <!-- 技术栈 -->
          <h3>我的一些技术栈🫡</h3>
          <div class="techStack">
            <div
              v-for="(i, index) in techStack.techStack"
              :key="index"
              class="techItem"
              :data-name="i.name"
            >
              <Icon :icon="i.icon" width="40" height="40" />
            </div>
          </div>
        </div>

        <div class="typew card">
          <Icon icon="carbon:quotes" width="16" height="16" />
          <Typewriter :text="typewriter" />
          <Icon icon="ph:quotes-fill" width="16" height="16" />
        </div>

        <!-- 博客订阅 -->
        <div class="blog-subscription card">
          <div class="blog-header">
            <h3>📝 最新博客文章</h3>
            <button 
              class="blog-refresh-btn" 
              @click="refreshBlogArticles" 
              :disabled="blogLoading"
              title="刷新文章"
            >
              <Icon 
                icon="mdi:refresh" 
                width="16" 
                height="16" 
                :class="{ 'rotating': blogLoading }" 
              />
            </button>
          </div>
          <div class="blog-articles">
            <div v-if="blogLoading" class="blog-loading">
              <Icon icon="eos-icons:loading" width="20" height="20" />
              正在加载文章...
            </div>
            <div v-else-if="blogError" class="blog-error">
              <Icon icon="material-symbols:error-outline" width="20" height="20" />
              {{ blogError }}
            </div>
            <div v-else-if="blogArticles.length === 0" class="blog-empty">
              暂无文章
            </div>
            <div v-else class="blog-list">
              <div 
                v-for="(article, index) in blogArticles" 
                :key="index"
                class="blog-item"
              >
                <a :href="article.link" target="_blank" class="blog-title">
                  {{ article.title }}
                </a>
                <div class="blog-meta">
                  <span class="blog-date">{{ formatDate(article.pubDate) }}</span>
                  <span class="blog-author">{{ article.author }}</span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="footer">
      <div class="footer-quote">
        <span v-if="isLoadingQuote" class="loading-text">加载中...</span>
        <span v-else>{{ currentQuote }}</span>
        <div class="quote-actions">
          <button class="action-btn" @click="refreshQuote" title="刷新一言" :disabled="isLoadingQuote">
            <Icon icon="mdi:refresh" width="12" height="12" :class="{ 'rotating': isLoadingQuote }" />
          </button>
          <button class="action-btn" @click="copyQuote" title="复制一言" :disabled="isLoadingQuote || !currentQuote">
            <Icon icon="mdi:content-copy" width="12" height="12" />
          </button>
        </div>
      </div>
      <div class="footer-info">
        <span>©{{ currentYear }} Handsome</span>
        <template v-if="footerConfig.icp">
          <span class="divider">|</span>
          <a :href="footerConfig.icpUrl" target="_blank">{{ footerConfig.icp }}</a>
        </template>
        <template v-if="footerConfig.police">
          <span class="divider">|</span>
          <a :href="footerConfig.policeUrl" target="_blank">{{ footerConfig.police }}</a>
        </template>
      </div>
    </div>
  </div>
</template>

<script setup>
import config from "../config/config.json";
import techStack from "../config/techStack.json";
import welcomeConfig from "../config/welcome.json";
import typewriter from "../config/typewriter.json";
import blogConfig from "../config/blog.json";
import footerConfig from "../config/footer.json";
import { Icon } from "@iconify/vue";
import { onMounted, ref, computed } from "vue";
import Typewriter from "../components/Typewriter.vue";

const now = ref(new Date());

// 随机一言
const currentQuote = ref("");
const isLoadingQuote = ref(false);

// 获取一言API
const getRandomQuote = async () => {
  try {
    isLoadingQuote.value = true;
    // 使用多个API源，提高成功率
    const apis = [
      'https://v1.hitokoto.cn/?c=a&c=b&c=c&c=d&c=e&c=f&c=g&c=h&c=i&c=j&c=k&c=l&c=m&c=n&c=o&c=p&c=q&c=r&c=s&c=t&c=u&c=v&c=w&c=x&c=y&c=z',
      'https://api.xygeng.cn/one',
      'https://api.uomg.com/api/rand.qinghua?format=json'
    ];
    
    // 随机选择一个API
    const randomApi = apis[Math.floor(Math.random() * apis.length)];
    
    const response = await fetch(randomApi);
    const data = await response.json();
    
    // 根据不同API的返回格式处理
    if (data.hitokoto) {
      // hitokoto.cn API
      currentQuote.value = data.hitokoto;
    } else if (data.content) {
      // xygeng.cn API
      currentQuote.value = data.content;
    } else if (data.data) {
      // uomg.com API
      currentQuote.value = data.data.content;
    } else {
      throw new Error('无法解析API返回数据');
    }
  } catch (error) {
    console.error('获取一言失败:', error);
    currentQuote.value = '网络连接失败，请稍后重试';
  } finally {
    isLoadingQuote.value = false;
  }
};

// 刷新一言
const refreshQuote = () => {
  getRandomQuote();
};

// 复制一言
const copyQuote = async () => {
  try {
    await navigator.clipboard.writeText(currentQuote.value);
  } catch (err) {
    console.error('复制失败:', err);
    // 降级方案：使用传统方法
    const textArea = document.createElement('textarea');
    textArea.value = currentQuote.value;
    document.body.appendChild(textArea);
    textArea.select();
    document.execCommand('copy');
    document.body.removeChild(textArea);
  }
};

// 动态获取当前年份
const currentYear = computed(() => now.value.getFullYear());

const hoursPassed = computed(() => now.value.getHours());
const hoursProgress = computed(() =>
  ((hoursPassed.value / 24) * 100).toFixed(2)
);

const daysInWeekPassed = computed(() => {
  const day = now.value.getDay();
  return day === 0 ? 7 : day;
});
const weekProgress = computed(() =>
  ((daysInWeekPassed.value / 7) * 100).toFixed(2)
);

const daysInMonthPassed = computed(() => now.value.getDate());
const daysInCurrentMonth = computed(() =>
  new Date(now.value.getFullYear(), now.value.getMonth() + 1, 0).getDate()
);
const monthProgress = computed(
  () => (daysInMonthPassed.value / daysInCurrentMonth.value) * 100
);

const daysInYearPassed = computed(() => {
  const startOfYear = new Date(now.value.getFullYear(), 0, 1);
  const diff = now.value - startOfYear;
  return Math.ceil(diff / (1000 * 60 * 60 * 24));
});

const daysInCurrentYear = computed(() => {
  const isLeap = isLeapYear(now.value.getFullYear());
  return isLeap ? 366 : 365;
});

const yearProgress = computed(
  () => (daysInYearPassed.value / daysInCurrentYear.value) * 100
);

function isLeapYear(year) {
  return (year % 4 === 0 && year % 100 !== 0) || year % 400 === 0;
}

// 欢迎信息相关变量
let ipLocation = {};

// 博客相关变量
const blogArticles = ref([]);
const blogLoading = ref(false);
const blogError = ref('');

// 获取距离函数
function getDistance(e1, n1, e2, n2) {
  const R = 6371;
  const { sin, cos, asin, PI, hypot } = Math;
  let getPoint = (e, n) => {
    e *= PI / 180;
    n *= PI / 180;
    return { x: cos(n) * cos(e), y: cos(n) * sin(e), z: sin(n) };
  };

  let a = getPoint(e1, n1);
  let b = getPoint(e2, n2);
  let c = hypot(a.x - b.x, a.y - b.y, a.z - b.z);
  let r = asin(c / 2) * 2 * R;
  return Math.round(r);
}

// 显示欢迎信息
function showWelcome() {
  if (ipLocation.locationText && ipLocation.locationText !== "未知") {
    let pos = ipLocation.locationText || ipLocation.country;
    let posdesc;
    
    // 计算距离
    let dist = 0;
    if (ipLocation.location && ipLocation.location.lng && ipLocation.location.lat) {
      dist = getDistance(welcomeConfig.locationLng, welcomeConfig.locationLat, ipLocation.location.lng, ipLocation.location.lat);
    } else {
      dist = 0; // 没有坐标时显示0
    }
    
    // 根据国家、省份、城市信息自定义欢迎语
    switch (ipLocation.country) {
      case "日本":
        posdesc = "よろしく，一起去看樱花吗";
        break;
      case "美国":
        posdesc = "Let us live in peace!";
        break;
      case "英国":
        posdesc = "想同你一起夜乘伦敦眼";
        break;
      case "俄罗斯":
        posdesc = "干了这瓶伏特加！";
        break;
      case "法国":
        posdesc = "C'est La Vie";
        break;
      case "德国":
        posdesc = "Die Zeit verging im Fluge.";
        break;
      case "澳大利亚":
        posdesc = "一起去大堡礁吧！";
        break;
      case "加拿大":
        posdesc = "拾起一片枫叶赠予你";
        break;
      case "中国":
        // 构建位置信息，包含区县
        const locationParts = [ipLocation.region, ipLocation.city];
        if (ipLocation.district && ipLocation.district !== "未知" && ipLocation.district.trim() !== "") {
          locationParts.push(ipLocation.district);
        }
        pos = locationParts.join(" ");
        switch (ipLocation.region.replace("省", "").replace("市", "")) {
          case "北京":
            posdesc = "北——京——欢迎你~~~";
            break;
          case "天津":
            posdesc = "讲段相声吧";
            break;
          case "河北":
            posdesc = "山势巍巍成壁垒，天下雄关铁马金戈由此向，无限江山";
            break;
          case "山西":
            posdesc = "展开坐具长三尺，已占山河五百余";
            break;
          case "内蒙古":
            posdesc = "天苍苍，野茫茫，风吹草低见牛羊";
            break;
          case "辽宁":
            posdesc = "我想吃烤鸡架！";
            break;
          case "吉林":
            posdesc = "状元阁就是东北烧烤之王";
            break;
          case "黑龙江":
            posdesc = "很喜欢哈尔滨大剧院";
            break;
          case "上海":
            posdesc = "众所周知，中国只有两个城市";
            break;
          case "江苏":
            switch (ipLocation.city.replace("市", "")) {
              case "南京":
                posdesc = "这是我挺想去的城市啦";
                break;
              case "苏州":
                posdesc = "上有天堂，下有苏杭";
                break;
              default:
                posdesc = "散装是必须要散装的";
                break;
            }
            break;
          case "浙江":
            posdesc = "东风渐绿西湖柳，雁已还人未南归";
            break;
          case "河南":
            switch (ipLocation.city.replace("市", "")) {
              case "郑州":
                posdesc = "豫州之域，天地之中";
                break;
              case "南阳":
                posdesc = "臣本布衣，躬耕于南阳此南阳非彼南阳！";
                break;
              case "驻马店":
                posdesc = "峰峰有奇石，石石挟仙气嵖岈山的花很美哦！";
                break;
              case "开封":
                posdesc = "刚正不阿包青天";
                break;
              case "洛阳":
                posdesc = "洛阳牡丹甲天下";
                break;
              case "周口":
                posdesc = "老子故里，道德之乡";
                break;
              default:
                posdesc = "可否带我品尝河南烩面啦？";
                break;
            }
            break;
          case "安徽":
            posdesc = "蚌埠住了，芜湖起飞";
            break;
          case "福建":
            posdesc = "井邑白云间，岩城远带山";
            break;
          case "江西":
            posdesc = "落霞与孤鹜齐飞，秋水共长天一色";
            break;
          case "山东":
            posdesc = "遥望齐州九点烟，一泓海水杯中泻";
            break;
          case "湖北":
            switch (ipLocation.city.replace("市", "")) {
              case "黄冈":
                posdesc = "红安将军县！辈出将才！";
                break;
              default:
                posdesc = "来碗热干面~";
                break;
            }
            break;
          case "湖南":
            posdesc = "74751，长沙斯塔克";
            break;
          case "广东":
            switch (ipLocation.city.replace("市", "")) {
              case "广州":
                posdesc = "看小蛮腰，喝早茶了嘛~";
                break;
              case "深圳":
                posdesc = "今天你逛商场了嘛~";
                break;
              case "阳江":
                posdesc = "阳春合水！博主家乡~ 欢迎来玩~";
                break;
              default:
                posdesc = "来两斤福建人~";
                break;
            }
            break;
          case "广西":
            posdesc = "桂林山水甲天下";
            break;
          case "海南":
            posdesc = "朝观日出逐白浪，夕看云起收霞光";
            break;
          case "四川":
            posdesc = "康康川妹子";
            break;
          case "贵州":
            posdesc = "茅台，学生，再塞200";
            break;
          case "云南":
            posdesc = "玉龙飞舞云缠绕，万仞冰川直耸天";
            break;
          case "西藏":
            posdesc = "躺在茫茫草原上，仰望蓝天";
            break;
          case "陕西":
            posdesc = "来份臊子面加馍";
            break;
          case "甘肃":
            switch (ipLocation.city.replace("市", "")) {
              case "兰州":
                posdesc = "来一碗兰州牛肉面🍝";
                break;
              case "武威":
                posdesc = "羌笛何须怨杨柳，春风不度玉门关";
                break;
              case "天水":
                posdesc = "大河之水天上来，奔流到海不复回";
                break;
              default:
                posdesc = "来甘肃旅游吧～";
                break;
            }
            break;
          case "青海":
            posdesc = "牛肉干和老酸奶都好好吃";
            break;
          case "宁夏":
            posdesc = "大漠孤烟直，长河落日圆";
            break;
          case "新疆":
            posdesc = "驼铃古道丝绸路，胡马犹闻唐汉风";
            break;
          case "台湾":
            posdesc = "我在这头，大陆在那头";
            break;
          case "香港":
            posdesc = "永定贼有残留地鬼嚎，迎击光非岁玉";
            break;
          case "澳门":
            posdesc = "性感荷官，在线发牌";
            break;
          default:
            posdesc = "带我去你的城市逛逛吧！";
            break;
        }
        break;
      default:
        posdesc = "带我去你的国家逛逛吧";
        break;
    }

    // 根据本地时间切换欢迎语
    let timeChange;
    let date = new Date();
    if (date.getHours() >= 5 && date.getHours() < 11) timeChange = "🌤️ 早上好，一日之计在于晨";
    else if (date.getHours() >= 11 && date.getHours() < 13) timeChange = "☀️ 中午好，记得午休喔~";
    else if (date.getHours() >= 13 && date.getHours() < 17) timeChange = "🕞 下午好，饮茶先啦！";
    else if (date.getHours() >= 17 && date.getHours() < 19) timeChange = "🚶‍♂️ 即将下班，记得按时吃饭~";
    else if (date.getHours() >= 19 && date.getHours() < 24) timeChange = "🌙 晚上好，夜生活嗨起来！";
    else timeChange = "夜深了，早点休息，少熬夜";


    try {
      const welcomeElement = document.getElementById("welcome-info");
      if (welcomeElement) {
        welcomeElement.innerHTML = `
          <div class="welcome-message">
            <p>${welcomeConfig.welcomeEmoji} 欢迎来自 <span class="highlight">${pos}</span> 的朋友</p>
            <p>${posdesc}</p>
            <p>距离约 <span class="highlight">${dist}</span> 公里</p>
            <p class="time-greeting">${timeChange}</p>
          </div>
        `;
      }
    } catch (err) {
      // 静默处理错误
    }
  } else {
    try {
      const welcomeElement = document.getElementById("welcome-info");
      if (welcomeElement) {
        welcomeElement.innerHTML = `<div class="error-message">${ipLocation.message || "获取位置信息失败"}</div>`;
      }
    } catch (err) {
      // 静默处理错误
    }
  }
}

onMounted(() => {
  setInterval(() => {
    now.value = new Date();
  }, 1000);

  // 获取位置信息 - 使用免费IP API
  getLocationInfo();
  
  // 获取博客文章
  getBlogArticles();
  
  // 初始化随机一言
  getRandomQuote();
  
  // 启动进度条动画
  setTimeout(() => {
    animateProgressBars();
  }, 1000);
});

// 进度条动画函数
function animateProgressBars() {
  const progressBars = document.querySelectorAll('.progress-left, .progress-right');
  progressBars.forEach((bar, index) => {
    const targetWidth = bar.style.width;
    bar.style.width = '0%';
    // 每个进度条延迟一点时间，避免同时开始
    setTimeout(() => {
      bar.style.width = targetWidth;
    }, index * 150 + 300);
  });
}

// 获取位置信息的函数
async function getLocationInfo() {
  try {
    // 使用cz88.net的IP API
    const response = await fetch('https://cz88.net/api/cz88/ip/base?ip=');
    const data = await response.json();
    
    if (data.success && data.code === 200) {
      // 构建位置信息，只显示到市一级
      let locationText = "";
      if (data.data.country === "中国") {
        // 只显示省份和城市，过滤掉空值
        const parts = [data.data.province, data.data.city].filter(part => part && part.trim() !== "");
        locationText = parts.join(" ");
      } else {
        locationText = data.data.country || "未知";
      }
      
      // 获取坐标信息
      let coordinates = null;
      if (data.data.locations && data.data.locations.length > 0) {
        // 使用第一个坐标点
        coordinates = {
          lng: parseFloat(data.data.locations[0].longitude),
          lat: parseFloat(data.data.locations[0].latitude)
        };
      }
      
      ipLocation = {
        city: data.data.city || "",
        country: data.data.country || "",
        region: data.data.province || "",
        district: data.data.districts || "",
        ip: data.data.ip || "未知",
        locationText: locationText,
        location: coordinates || {
          lng: welcomeConfig.locationLng,
          lat: welcomeConfig.locationLat,
        }
      };
    } else {
      throw new Error('API返回错误: ' + data.message);
    }
  } catch (error) {
    // 备用方案：显示默认信息
    ipLocation = {
      city: "",
      country: "",
      region: "",
      district: "",
      ip: "未知",
      locationText: "未知",
      location: {
        lng: welcomeConfig.locationLng,
        lat: welcomeConfig.locationLat,
      }
    };
  }
  
  showWelcome();
}

// 获取博客文章
async function getBlogArticles(forceRefresh = false) {
  blogLoading.value = true;
  blogError.value = '';
  
  try {
    // 检查缓存
    const cacheKey = 'blog_articles_cache';
    const cacheTimeKey = 'blog_articles_cache_time';
    const now = Date.now();
    const oneDay = 24 * 60 * 60 * 1000; // 一天的毫秒数
    
    // 获取缓存数据和时间
    const cachedData = localStorage.getItem(cacheKey);
    const cachedTime = localStorage.getItem(cacheTimeKey);
    
    // 如果缓存存在且未过期（24小时内）且不是强制刷新
    if (!forceRefresh && cachedData && cachedTime && (now - parseInt(cachedTime)) < oneDay) {
      blogArticles.value = JSON.parse(cachedData);
      blogLoading.value = false;
      return;
    }
    
    // 缓存不存在或已过期或强制刷新，重新获取
    const response = await fetch(`${blogConfig.apiUrl}?token=${blogConfig.token}&url=${encodeURIComponent(blogConfig.rssUrl)}`);
    const data = await response.json();
    
    if (data.code === 200 && data.data && data.data.items) {
      const articles = data.data.items.slice(0, blogConfig.maxArticles);
      blogArticles.value = articles;
      
      // 保存到缓存
      localStorage.setItem(cacheKey, JSON.stringify(articles));
      localStorage.setItem(cacheTimeKey, now.toString());
    } else {
      throw new Error(data.message || '获取博客文章失败');
    }
  } catch (error) {
    console.error('获取博客文章失败:', error);
    
    // 如果API失败，尝试使用缓存数据（即使过期）
    const cacheKey = 'blog_articles_cache';
    const cachedData = localStorage.getItem(cacheKey);
    if (cachedData) {
      blogArticles.value = JSON.parse(cachedData);
      blogError.value = '使用缓存数据，可能不是最新内容';
    } else {
      blogError.value = '加载文章失败，请稍后重试';
    }
  } finally {
    blogLoading.value = false;
  }
}

// 强制刷新博客文章
const refreshBlogArticles = () => {
  getBlogArticles(true);
};

// 格式化日期
function formatDate(dateString) {
  try {
    const date = new Date(dateString);
    const now = new Date();
    const diffTime = now - date;
    const diffDays = Math.floor(diffTime / (1000 * 60 * 60 * 24));
    
    if (diffDays === 0) {
      return '今天';
    } else if (diffDays === 1) {
      return '昨天';
    } else if (diffDays < 7) {
      return `${diffDays}天前`;
    } else {
      return date.toLocaleDateString('zh-CN', {
        year: 'numeric',
        month: 'short',
        day: 'numeric'
      });
    }
  } catch (error) {
    return dateString;
  }
}
</script>

<style>
@import url(../assets/css/MainCard.css);
</style>
