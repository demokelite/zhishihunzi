<template>
  <div class="home-container">
    <!-- 顶部导航栏 -->
    <HeaderNav />
    
    <!-- 英雄区域 -->
    <section class="hero-section">
      <div class="hero-content">
        <h1 class="hero-title">欢迎来到我的个人网站</h1>
        <p class="hero-subtitle">探索我的作品和故事</p>
        <div class="hero-buttons">
          <el-button 
            type="primary" 
            size="large"
            @click="navigateToPortfolio"
            class="btn-animated"
          >
            查看我的作品
          </el-button>
          <el-button 
            size="large"
            @click="navigateToAbout"
            class="btn-animated"
          >
            了解更多
          </el-button>
        </div>
      </div>
    </section>

    <!-- 特色内容 -->
    <section class="features-section">
      <div class="section-title">
        <h2>我的技能与服务</h2>
        <div class="title-underline"></div>
      </div>
      
      <div class="features-grid">
        <div class="feature-card" v-for="feature in features" :key="feature.id">
          <div class="feature-icon">
            <i :class="feature.icon" :style="{ color: feature.color }"></i>
          </div>
          <h3>{{ feature.title }}</h3>
          <p>{{ feature.description }}</p>
        </div>
      </div>
    </section>

    <!-- 精选作品（已注释） -->
    <!-- 
    <section class="portfolio-preview-section">
      <div class="section-title">
        <h2>精选作品</h2>
        <div class="title-underline"></div>
        <el-button 
          type="text"
          @click="navigateToPortfolio"
          class="view-all-btn"
        >
          查看全部 <i class="el-icon-arrow-right"></i>
        </el-button>
      </div>
      
      <div class="portfolio-grid">
        <div class="portfolio-item" v-for="item in portfolioItems" :key="item.id">
          <div class="portfolio-image">
            <img :src="item.image" :alt="item.title" />
            <div class="portfolio-overlay">
              <h4>{{ item.title }}</h4>
              <p>{{ item.category }}</p>
            </div>
          </div>
        </div>
      </div>
    </section>
    -->

    <!-- 客户评价（已注释） -->
    <!-- 
    <section class="testimonials-section">
      <div class="section-title">
        <h2>客户评价</h2>
        <div class="title-underline"></div>
      </div>
      
      <div class="testimonials-slider">
        <div class="testimonial-item" v-for="testimonial in testimonials" :key="testimonial.id">
          <i class="el-icon-quote-left quote-icon"></i>
          <p class="testimonial-text">{{ testimonial.content }}</p>
          <div class="testimonial-author">
            <img :src="testimonial.avatar" :alt="testimonial.name" class="author-avatar" />
            <div>
              <h4>{{ testimonial.name }}</h4>
              <p>{{ testimonial.position }}</p>
            </div>
          </div>
        </div>
      </div>
    </section>
    -->

    <!-- 页脚 -->
    <Footer />
  </div>
</template>

<script>
import HeaderNav from '@/components/HeaderNav.vue';
import Footer from '@/components/Footer.vue';

export default {
  name: 'Home',
  components: {
    HeaderNav,
    Footer
  },
  data() {
    return {
      features: [
        {
          id: 1,
          title: 'Web开发',
          description: '使用现代前端技术构建响应式、高性能的网站和应用程序',
          icon: 'el-icon-code',
          color: '#409EFF'
        },
        {
          id: 2,
          title: 'UI/UX设计',
          description: '创造美观且用户友好的界面，提供出色的用户体验',
          icon: 'el-icon-paintbrush',
          color: '#67C23A'
        },
        {
          id: 3,
          title: '移动应用',
          description: '开发跨平台的移动应用，确保在各种设备上都有出色的表现',
          icon: 'el-icon-mobile',
          color: '#E6A23C'
        },
        {
          id: 4,
          title: '后端开发',
          description: '构建稳定、安全的后端服务，支持前端应用的各种需求',
          icon: 'el-icon-server',
          color: '#F56C6C'
        }
      ],
      // 精选作品数据（已注释）
      /*
      portfolioItems: [
        {
          id: 1,
          title: '电子商务网站',
          category: 'Web开发',
          image: 'https://picsum.photos/id/0/800/600'
        },
        {
          id: 2,
          title: '企业管理系统',
          category: '应用开发',
          image: 'https://picsum.photos/id/1/800/600'
        },
        {
          id: 3,
          title: '个人博客平台',
          category: 'Web开发',
          image: 'https://picsum.photos/id/2/800/600'
        }
      ],
      // 客户评价数据（已注释）
      testimonials: [
        {
          id: 1,
          content: '这位开发者的工作非常出色，他们不仅按时完成了项目，而且质量超出了我们的预期。强烈推荐！',
          name: '张经理',
          position: '技术总监 @ ABC公司',
          avatar: 'https://picsum.photos/id/1001/100/100'
        },
        {
          id: 2,
          content: '与他们合作是一种享受，他们理解需求的能力和解决问题的方法给我留下了深刻的印象。',
          name: '李总监',
          position: '产品经理 @ XYZ公司',
          avatar: 'https://picsum.photos/id/1002/100/100'
        }
      ]
      */
    };
  },
  mounted() {
    // 添加页面进入动画
    const heroSection = document.querySelector('.hero-section');
    heroSection.classList.add('animate-in');
  },
  methods: {
    navigateToPortfolio() {
      this.$router.push('/portfolio');
    },
    navigateToAbout() {
      this.$router.push('/about');
    }
  }
};
</script>

<style scoped>
.home-container {
  font-family: 'Arial', sans-serif;
}

/* 英雄区域样式 */
.hero-section {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  text-align: center;
  padding: 0 20px;
  opacity: 0;
  transform: translateY(20px);
  transition: opacity 0.8s ease, transform 0.8s ease;
}

.hero-section.animate-in {
  opacity: 1;
  transform: translateY(0);
}

.hero-content {
  max-width: 800px;
}

.hero-title {
  font-size: 48px;
  margin-bottom: 20px;
  font-weight: bold;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.hero-subtitle {
  font-size: 24px;
  margin-bottom: 40px;
  opacity: 0.9;
}

.hero-buttons {
  display: flex;
  gap: 20px;
  justify-content: center;
}

.btn-animated {
  transition: all 0.3s ease;
}

.btn-animated:hover {
  transform: translateY(-3px);
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.15);
}

/* 通用章节标题样式 */
.section-title {
  text-align: center;
  margin-bottom: 60px;
  position: relative;
}

.section-title h2 {
  font-size: 36px;
  color: #333;
  margin-bottom: 10px;
  font-weight: bold;
}

.title-underline {
  width: 80px;
  height: 3px;
  background: #409EFF;
  margin: 0 auto;
  position: relative;
}

.title-underline::after {
  content: '';
  position: absolute;
  width: 40px;
  height: 3px;
  background: #667eea;
  top: 0;
  left: 50%;
  transform: translateX(-50%);
}

/* 特色内容样式 */
.features-section {
  padding: 100px 0;
  background: #f8f9fa;
}

.features-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 30px;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

.feature-card {
  background: white;
  padding: 30px;
  border-radius: 10px;
  text-align: center;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
}

.feature-card:hover {
  transform: translateY(-10px);
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
}

.feature-icon {
  font-size: 48px;
  margin-bottom: 20px;
}

.feature-card h3 {
  font-size: 20px;
  margin-bottom: 15px;
  color: #333;
}

.feature-card p {
  color: #666;
  line-height: 1.6;
}

/* 精选作品和客户评价样式已移除 */
/* 如需恢复，请参考原始代码 */

/* 响应式设计 */
@media (max-width: 768px) {
  .hero-title {
    font-size: 36px;
  }
  
  .hero-subtitle {
    font-size: 18px;
  }
  
  .hero-buttons {
    flex-direction: column;
    align-items: center;
  }
  
  .section-title h2 {
    font-size: 28px;
  }
  
  .features-grid,
  .portfolio-grid {
    grid-template-columns: 1fr;
  }
}
</style>