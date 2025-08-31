<template>
  <div class="blog-container">
    <!-- 顶部导航栏 -->
    <HeaderNav />
    
    <!-- 页面标题 -->
    <section class="page-title-section">
      <div class="container">
        <h1>我的博客</h1>
        <p>分享我的技术心得和生活感悟</p>
      </div>
    </section>

    <!-- 博客内容区 -->
    <section class="blog-content-section">
      <div class="container">
        <div class="blog-layout">
          <!-- 博客文章列表 -->
          <div class="blog-main">
            <div class="blog-posts">
              <div 
                class="blog-post" 
                v-for="post in paginatedPosts" 
                :key="post.id"
                @click="openPostDetail(post)"
              >
                <div class="post-image">
                  <img :src="post.image" :alt="post.title" />
                </div>
                <div class="post-content">
                  <div class="post-meta">
                    <span class="post-date">{{ post.date }}</span>
                    <span class="post-category">{{ post.category }}</span>
                  </div>
                  <h2 class="post-title">{{ post.title }}</h2>
                  <p class="post-excerpt">{{ post.excerpt }}</p>
                  <div class="post-footer">
                    <div class="post-author">
                      <img src="https://picsum.photos/id/1005/50/50" alt="作者头像" />
                      <span>{{ post.author }}</span>
                    </div>
                    <div class="post-stats">
                      <span><i class="el-icon-view"></i> {{ post.views }}</span>
                      <span><i class="el-icon-comment"></i> {{ post.comments }}</span>
                    </div>
                  </div>
                </div>
              </div>
            </div>
            
            <!-- 分页 -->
            <div class="pagination">
              <el-pagination
                background
                layout="prev, pager, next, total"
                :total="posts.length"
                :page-size="postsPerPage"
                v-model="currentPage"
                @current-change="handlePageChange"
              />
            </div>
          </div>
          
          <!-- 侧边栏 -->
          <div class="blog-sidebar">
            <!-- 搜索框 -->
            <div class="sidebar-widget search-widget">
              <h3>搜索</h3>
              <el-input
                v-model="searchQuery"
                placeholder="输入关键词搜索"
                prefix-icon="el-icon-search"
                @keyup.enter.native="searchPosts"
              />
              <el-button 
                type="primary" 
                style="width: 100%; margin-top: 10px;"
                @click="searchPosts"
              >
                搜索
              </el-button>
            </div>
            
            <!-- 分类 -->
            <div class="sidebar-widget categories-widget">
              <h3>分类</h3>
              <ul>
                <li 
                  v-for="category in categories" 
                  :key="category.id"
                  :class="{ active: activeCategory === category.value }"
                  @click="filterByCategory(category.value)"
                >
                  <span class="category-name">{{ category.name }}</span>
                  <span class="category-count">{{ getCategoryCount(category.value) }}</span>
                </li>
              </ul>
            </div>
            
            <!-- 标签云 -->
            <div class="sidebar-widget tags-widget">
              <h3>标签</h3>
              <div class="tags-cloud">
                <span 
                  class="tag" 
                  v-for="tag in tags" 
                  :key="tag.id"
                  :class="{ active: activeTag === tag.value }"
                  @click="filterByTag(tag.value)"
                >
                  {{ tag.name }}
                </span>
              </div>
            </div>
            
            <!-- 热门文章 -->
            <div class="sidebar-widget popular-posts-widget">
              <h3>热门文章</h3>
              <div class="popular-post" v-for="post in popularPosts" :key="post.id">
                <div class="popular-post-image">
                  <img :src="post.image" :alt="post.title" />
                </div>
                <div class="popular-post-content">
                  <h4>{{ post.title }}</h4>
                  <span class="popular-post-date">{{ post.date }}</span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- 文章详情弹窗 -->
    <el-dialog 
      :visible.sync="dialogVisible" 
      width="80%" 
      :show-close="false"
      class="post-detail-dialog"
    >
      <div class="dialog-header">
        <div class="post-meta">
          <span class="post-date">{{ selectedPost?.date }}</span>
          <span class="post-category">{{ getCategoryName(selectedPost?.category) }}</span>
        </div>
        <h2>{{ selectedPost?.title }}</h2>
        <div class="post-author">
          <img src="https://picsum.photos/id/1005/50/50" alt="作者头像" />
          <div>
            <span class="author-name">{{ selectedPost?.author }}</span>
            <span class="author-title">全栈开发者</span>
          </div>
        </div>
        <el-button type="text" @click="closePostDetail" class="close-button">
          <i class="el-icon-close"></i>
        </el-button>
      </div>
      
      <div class="post-detail-content">
        <div class="detail-image">
          <img :src="selectedPost?.image" :alt="selectedPost?.title" />
        </div>
        
        <div class="detail-text" v-html="selectedPost?.content"></div>
        
        <div class="detail-tags">
          <span 
            class="tag" 
            v-for="tagId in selectedPost?.tags" 
            :key="tagId"
          >
            {{ getTagName(tagId) }}
          </span>
        </div>
        
        <!-- 评论区 -->
        <div class="comments-section">
          <h3>评论 ({{ selectedPost?.comments || 0 }})</h3>
          <div class="comment-input">
            <el-input
              type="textarea"
              :rows="4"
              placeholder="写下你的评论..."
              v-model="commentText"
            />
            <el-button 
              type="primary" 
              style="margin-top: 10px;"
              @click="submitComment"
              :disabled="!commentText.trim()"
            >
              发表评论
            </el-button>
          </div>
          
          <div class="comments-list">
            <div class="comment-item" v-for="comment in selectedPost?.commentsList" :key="comment.id">
              <img :src="comment.avatar" :alt="comment.name" class="comment-avatar" />
              <div class="comment-content">
                <div class="comment-header">
                  <span class="comment-author">{{ comment.name }}</span>
                  <span class="comment-date">{{ comment.date }}</span>
                </div>
                <p class="comment-text">{{ comment.content }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </el-dialog>

    <!-- 页脚 -->
    <Footer />
  </div>
</template>

<script>
import HeaderNav from '@/components/HeaderNav.vue';
import Footer from '@/components/Footer.vue';

export default {
  name: 'Blog',
  components: {
    HeaderNav,
    Footer
  },
  data() {
    return {
      searchQuery: '',
      activeCategory: 'all',
      activeTag: '',
      currentPage: 1,
      postsPerPage: 3,
      dialogVisible: false,
      selectedPost: null,
      commentText: '',
      categories: [
        { id: 1, name: '全部', value: 'all' },
        { id: 2, name: '技术', value: 'tech' },
        { id: 3, name: '生活', value: 'life' },
        { id: 4, name: '旅行', value: 'travel' },
        { id: 5, name: '学习', value: 'study' }
      ],
      tags: [
        { id: 1, name: 'Vue.js', value: 'vue' },
        { id: 2, name: 'JavaScript', value: 'javascript' },
        { id: 3, name: 'React', value: 'react' },
        { id: 4, name: 'Node.js', value: 'node' },
        { id: 5, name: 'CSS', value: 'css' },
        { id: 6, name: '前端', value: 'frontend' },
        { id: 7, name: '后端', value: 'backend' },
        { id: 8, name: '生活方式', value: 'lifestyle' }
      ],
      posts: [
        {
          id: 1,
          title: 'Vue 3 组合式API实践指南',
          excerpt: '探索Vue 3组合式API的强大功能，以及如何在实际项目中应用...',
          content: '<p>Vue 3的组合式API为我们提供了一种更灵活、更强大的方式来组织和重用组件逻辑。在本文中，我将分享我的实践经验，包括如何使用setup函数、ref和reactive、computed和watch等核心API，以及如何构建可重用的组合函数。</p><p>组合式API的主要优势在于它可以更好地组织相关的逻辑代码，使得代码更易于理解和维护。特别是对于大型组件，组合式API可以将相关功能的代码组织在一起，而不是分散在不同的选项中。</p><p>在实际项目中，我发现使用组合式API可以显著提高代码的可重用性和可维护性。通过创建自定义的组合函数，我们可以将常用的逻辑抽象出来，然后在多个组件中复用。</p><p>此外，组合式API还提供了更好的TypeScript支持，使得类型推断更加准确，开发体验更加流畅。如果你还没有尝试过Vue 3的组合式API，我强烈建议你在你的下一个项目中试试看！</p>',
          image: 'https://picsum.photos/id/180/800/500',
          date: '2023年6月15日',
          category: 'tech',
          tags: ['vue', 'javascript', 'frontend'],
          author: '张明',
          views: 1243,
          comments: 35,
          commentsList: [
            {
              id: 1,
              name: '李同学',
              avatar: 'https://picsum.photos/id/1012/50/50',
              date: '2023年6月16日',
              content: '非常实用的指南，帮助我解决了很多实际问题，谢谢分享！'
            },
            {
              id: 2,
              name: '王开发',
              avatar: 'https://picsum.photos/id/1025/50/50',
              date: '2023年6月17日',
              content: '组合式API确实比选项式API更灵活，尤其是在大型项目中。'
            }
          ]
        },
        {
          id: 2,
          title: '前端性能优化的10个实用技巧',
          excerpt: '了解如何优化你的前端应用性能，提升用户体验...',
          content: '<p>前端性能优化是一个永恒的话题，也是每个前端开发者必须面对的挑战。在本文中，我将分享10个实用的前端性能优化技巧，帮助你提升你的应用性能和用户体验。</p><h3>1. 减少HTTP请求</h3><p>HTTP请求是前端性能的主要瓶颈之一。通过合并文件、使用CSS Sprites、字体图标等方式，可以显著减少HTTP请求的数量。</p><h3>2. 使用CDN</h3><p>内容分发网络(CDN)可以将你的静态资源分发到全球各地的服务器上，使用户可以从最近的服务器获取资源，从而减少加载时间。</p><h3>3. 压缩和最小化资源</h3><p>压缩HTML、CSS和JavaScript文件可以减少文件大小，加快加载速度。此外，删除不必要的代码和注释，也可以进一步减小文件体积。</p><h3>4. 延迟加载非关键资源</h3><p>对于不是立即需要的资源，可以使用延迟加载技术，在用户需要时再加载，从而减少初始加载时间。</p><h3>5. 使用缓存策略</h3><p>合理使用浏览器缓存和HTTP缓存，可以避免重复加载资源，显著提升重复访问时的性能。</p><h3>6. 优化图片</h3><p>图片是网页中体积最大的资源之一。通过选择合适的图片格式、压缩图片、使用响应式图片等方式，可以优化图片加载性能。</p><h3>7. 减少DOM操作</h3><p>DOM操作是非常耗费性能的。通过减少DOM操作、使用虚拟DOM、批量更新DOM等方式，可以提升JavaScript执行效率。</p><h3>8. 使用Web Workers</h3><p>对于耗时的计算任务，可以使用Web Workers在后台线程中执行，避免阻塞主线程，提升用户体验。</p><h3>9. 优化CSS选择器</h3><p>CSS选择器的效率会影响页面的渲染性能。使用更高效的选择器，避免过度嵌套，可以提升CSS解析和渲染效率。</p><h3>10. 监控和分析性能</h3><p>最后，持续监控和分析你的应用性能，找出性能瓶颈，并持续优化，是保持良好性能的关键。</p>',
          image: 'https://picsum.photos/id/1/800/500',
          date: '2023年6月8日',
          category: 'tech',
          tags: ['performance', 'frontend', 'javascript'],
          author: '张明',
          views: 2156,
          comments: 47,
          commentsList: [
            {
              id: 1,
              name: '张设计师',
              avatar: 'https://picsum.photos/id/1014/50/50',
              date: '2023年6月9日',
              content: '非常全面的优化指南，特别是图片优化部分对我帮助很大。'
            }
          ]
        },
        {
          id: 3,
          title: '我的程序员远程工作经验分享',
          excerpt: '作为一名远程工作的程序员，我想分享一些提高效率和保持工作生活平衡的经验...',
          content: '<p>远程工作已经成为越来越多程序员的选择，尤其是在疫情之后。作为一名有3年远程工作经验的程序员，我想分享一些我的经验和心得，希望能帮助到正在或准备远程工作的朋友们。</p><h3>建立专门的工作空间</h3><p>在家工作的最大挑战之一就是工作和生活的界限模糊。建立一个专门的工作空间，可以帮助你进入工作状态，也有助于在工作结束后切换到生活模式。</p><h3>制定规律的作息时间</h3><p>虽然远程工作给了我们更多的灵活性，但保持规律的作息时间仍然非常重要。制定一个合理的时间表，包括工作时间、休息时间和运动时间，并尽量遵守。</p><h3>使用高效的沟通工具</h3><p>远程工作中，沟通尤为重要。选择合适的沟通工具，如Slack、Zoom、Microsoft Teams等，并建立良好的沟通习惯，可以避免很多误解和问题。</p><h3>设置明确的目标和计划</h3><p>每天开始工作前，制定明确的目标和计划，并定期检查进度，可以帮助你保持专注和高效。使用项目管理工具如Trello、Jira等，可以更好地组织和跟踪工作。</p><h3>保持与团队的联系</h3><p>远程工作容易让人感到孤独。定期与团队成员进行视频会议、聊天，分享工作和生活，可以帮助你保持团队归属感和工作动力。</p><h3>注意身心健康</h3><p>远程工作可能会导致久坐不动，缺乏运动。定期起身活动，进行适量的运动，保持良好的饮食习惯和充足的睡眠，对于保持身心健康至关重要。</p><h3>持续学习和成长</h3><p>远程工作给了我们更多的自由时间，可以用来学习新技能和知识。持续学习和成长，不仅可以提升你的职业竞争力，也可以让你的工作更加有趣和有挑战性。</p><p>远程工作既有挑战，也有机遇。通过合理的规划和良好的习惯，你可以充分发挥远程工作的优势，提高工作效率，同时享受更多的自由和灵活性。</p>',
          image: 'https://picsum.photos/id/160/800/500',
          date: '2023年5月25日',
          category: 'life',
          tags: ['remote', 'work', 'lifestyle'],
          author: '张明',
          views: 1876,
          comments: 52,
          commentsList: [
            {
              id: 1,
              name: '赵远程',
              avatar: 'https://picsum.photos/id/1027/50/50',
              date: '2023年5月26日',
              content: '非常实用的经验分享，我也是一名远程工作者，这些建议对我很有帮助！'
            }
          ]
        },
        {
          id: 4,
          title: '探索JavaScript的异步编程模型',
          excerpt: '深入了解JavaScript的异步编程模型，包括回调函数、Promise和async/await...',
          content: '<p>JavaScript是一门单线程语言，但它通过异步编程模型实现了非阻塞的I/O操作。在本文中，我将深入探讨JavaScript的异步编程模型，从早期的回调函数，到Promise，再到现代的async/await语法糖。</p><h3>回调函数</h3><p>回调函数是JavaScript中最基本的异步编程方式。它通过将一个函数作为参数传递给另一个函数，当异步操作完成时，调用这个函数来处理结果。然而，回调函数容易导致"回调地狱"(Callback Hell)，使代码难以理解和维护。</p><h3>Promise</h3><p>Promise是ES6引入的一种处理异步操作的新方式。它通过链式调用解决了回调地狱的问题，使异步代码更加清晰和易于理解。Promise有三种状态：pending(进行中)、fulfilled(已成功)和rejected(已失败)。</p><h3>async/await</h3><p>async/await是ES2017引入的语法糖，它基于Promise，使异步代码看起来更像同步代码，进一步提高了代码的可读性和可维护性。async函数返回一个Promise，await关键字用于等待一个Promise的解决。</p><h3>实际应用案例</h3><p>在实际项目中，我们通常会结合使用这些异步编程方式。例如，对于简单的异步操作，我们可以使用回调函数；对于复杂的异步流程，我们可以使用Promise链式调用或async/await语法。</p><p>理解JavaScript的异步编程模型，对于编写高效、可靠的JavaScript代码至关重要。通过掌握这些异步编程方式，你可以更好地处理JavaScript中的异步操作，避免常见的陷阱和问题。</p>',
          image: 'https://picsum.photos/id/0/800/500',
          date: '2023年5月18日',
          category: 'tech',
          tags: ['javascript', 'async', 'promise'],
          author: '张明',
          views: 3245,
          comments: 67,
          commentsList: []
        },
        {
          id: 5,
          title: '我的2023年技术学习计划',
          excerpt: '分享我2023年的技术学习目标和计划，包括新框架、新语言和新技术的学习...',
          content: '<p>技术发展日新月异，作为一名程序员，持续学习是必不可少的。在本文中，我想分享我的2023年技术学习计划，希望能给你一些启发。</p><h3>前端技术</h3><p>在前端领域，我计划深入学习Vue 3和React的最新特性，特别是Vue 3的组合式API和React的Hooks。此外，我还想学习一些新兴的前端框架，如Svelte和Solid.js，了解它们的设计理念和优势。</p><h3>后端技术</h3><p>在后端方面，我计划学习Node.js的最新特性和最佳实践，特别是在微服务架构和性能优化方面。我还想学习Go语言，因为它在高并发和云原生应用方面有很大的优势。</p><h3>云原生和DevOps</h3><p>云原生和DevOps是现代软件开发的重要趋势。我计划深入学习Docker和Kubernetes，掌握容器化和容器编排技术。此外，我还想学习CI/CD工具和实践，如GitHub Actions和Jenkins，提高开发和部署效率。</p><h3>数据科学和AI</h3><p>数据科学和人工智能是当前最热门的技术领域之一。我计划学习Python的数据处理库，如Pandas和NumPy，以及机器学习框架，如TensorFlow和PyTorch，了解AI的基本概念和应用。</p><h3>软技能</h3><p>除了技术技能外，软技能也同样重要。我计划提高我的沟通能力、团队协作能力和项目管理能力，学习敏捷开发和Scrum方法论，提升我的综合竞争力。</p><h3>学习方法</h3><p>为了有效地实现这些学习目标，我计划采用以下学习方法：</p><ul><li>制定详细的学习计划和时间表</li><li>结合理论学习和实践项目</li><li>参加在线课程和技术社区活动</li><li>阅读技术书籍和博客文章</li><li>定期总结和分享学习成果</li></ul><p>学习是一个终身的过程，我相信通过持续的学习和实践，我可以不断提升自己的技术水平和职业竞争力。如果你也有类似的学习计划，欢迎在评论区分享，我们可以互相鼓励和学习。</p>',
          image: 'https://picsum.photos/id/3/800/500',
          date: '2023年1月5日',
          category: 'study',
          tags: ['learning', 'career', 'frontend', 'backend'],
          author: '张明',
          views: 2890,
          comments: 89,
          commentsList: []
        },
        {
          id: 6,
          title: '日本关西七日游攻略',
          excerpt: '分享我的日本关西地区七日游经历和攻略，包括大阪、京都、奈良等地的景点推荐...',
          content: '<p>日本一直是我向往的旅行目的地，今年终于有机会去关西地区旅行了七天。在这篇文章中，我想分享我的旅行经历和一些实用的攻略，希望能帮助到计划去日本旅行的朋友们。</p><h3>行程规划</h3><p>我的行程安排是：大阪3天，京都2天，奈良1天，最后回到大阪1天。这样的安排比较宽松，不会太赶，可以充分体验每个地方的特色。</p><h3>大阪</h3><p>大阪是关西地区的交通枢纽，也是一个充满活力的城市。我推荐的必去景点包括：</p><ul><li>大阪城公园：欣赏雄伟的大阪城和美丽的公园景色</li><li>道顿堀：体验大阪的美食和夜生活</li><li>环球影城：适合全家人游玩的主题公园</li><li>梅田蓝天大厦：俯瞰大阪全景的绝佳地点</li></ul><h3>京都</h3><p>京都是日本传统文化的代表，拥有众多的寺庙和神社。我推荐的必去景点包括：</p><ul><li>清水寺：京都最著名的寺庙之一，春季樱花和秋季红叶都非常美丽</li><li>金阁寺：贴满金箔的三层楼阁，非常壮观</li><li>岚山竹林：电影《卧虎藏龙》的取景地，竹林小径非常有意境</li><li>伏见稻荷大社：千座朱红色鸟居组成的隧道，非常震撼</li></ul><h3>奈良</h3><p>奈良是日本的古都，以鹿群和历史遗迹而闻名。我推荐的必去景点包括：</p><ul><li>奈良公园：与可爱的鹿群互动，喂食鹿仙贝</li><li>东大寺：世界上最大的木结构建筑，寺内有巨大的佛像</li><li>春日大社：被联合国教科文组织列为世界文化遗产</li></ul><h3>交通</h3><p>关西地区的交通非常发达，我建议购买关西地区铁路通票(Kansai Thru Pass)，可以无限次乘坐关西地区的地铁和私铁，非常方便和划算。</p><h3>住宿</h3><p>我在大阪住在心斋桥附近，交通便利，购物和美食都很方便。在京都住在四条河原町附近，离各个景点都比较近。</p><h3>美食</h3><p>日本的美食种类繁多，我推荐尝试的包括：</p><ul><li>大阪烧：大阪的特色美食，类似煎饼但更加丰富</li><li>拉面：日本的经典面食，各个地区有不同的特色</li><li>寿司：新鲜的生鱼片配上米饭，口感非常棒</li><li>居酒屋：体验日本的传统酒馆文化</li></ul><p>这次日本之行让我收获颇丰，不仅欣赏了美丽的景色，还体验了丰富的文化和美食。如果你也有机会去日本旅行，一定不要错过关西地区的这些精彩景点！</p>',
          image: 'https://picsum.photos/id/164/800/500',
          date: '2023年4月10日',
          category: 'travel',
          tags: ['japan', 'travel', 'culture'],
          author: '张明',
          views: 3678,
          comments: 102,
          commentsList: []
        }
      ]
    };
  },
  computed: {
    filteredPosts() {
      let result = [...this.posts];
      
      // 按分类筛选
      if (this.activeCategory !== 'all') {
        result = result.filter(post => post.category === this.activeCategory);
      }
      
      // 按标签筛选
      if (this.activeTag) {
        result = result.filter(post => post.tags.includes(this.activeTag));
      }
      
      // 搜索筛选
      if (this.searchQuery) {
        const query = this.searchQuery.toLowerCase();
        result = result.filter(post => 
          post.title.toLowerCase().includes(query) ||
          post.excerpt.toLowerCase().includes(query) ||
          post.content.toLowerCase().includes(query)
        );
      }
      
      return result;
    },
    paginatedPosts() {
      const startIndex = (this.currentPage - 1) * this.postsPerPage;
      return this.filteredPosts.slice(startIndex, startIndex + this.postsPerPage);
    },
    popularPosts() {
      return [...this.posts]
        .sort((a, b) => b.views - a.views)
        .slice(0, 3);
    }
  },
  mounted() {
    // 添加页面进入动画
    const sections = document.querySelector('.blog-content-section');
    
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          entry.target.classList.add('animate-in');
        }
      });
    }, { threshold: 0.1 });
    
    observer.observe(sections);
  },
  beforeDestroy() {
    // 确保离开页面时恢复背景滚动
    document.body.style.overflow = 'auto';
  },
  methods: {
    searchPosts() {
      this.currentPage = 1;
      // 滚动到文章列表
      const blogMain = document.querySelector('.blog-main');
      blogMain.scrollIntoView({ behavior: 'smooth' });
    },
    filterByCategory(category) {
      this.activeCategory = category;
      this.activeTag = '';
      this.searchQuery = '';
      this.currentPage = 1;
      // 滚动到文章列表
      const blogMain = document.querySelector('.blog-main');
      blogMain.scrollIntoView({ behavior: 'smooth' });
    },
    filterByTag(tag) {
      this.activeTag = this.activeTag === tag ? '' : tag;
      this.currentPage = 1;
      // 滚动到文章列表
      const blogMain = document.querySelector('.blog-main');
      blogMain.scrollIntoView({ behavior: 'smooth' });
    },
    handlePageChange(page) {
      this.currentPage = page;
      // 滚动到文章列表顶部
      const blogPosts = document.querySelector('.blog-posts');
      blogPosts.scrollIntoView({ behavior: 'smooth' });
    },
    getCategoryCount(category) {
      if (category === 'all') {
        return this.posts.length;
      }
      return this.posts.filter(post => post.category === category).length;
    },
    getCategoryName(categoryValue) {
      const category = this.categories.find(c => c.value === categoryValue);
      return category ? category.name : '';
    },
    getTagName(tagValue) {
      const tag = this.tags.find(t => t.value === tagValue);
      return tag ? tag.name : '';
    },
    openPostDetail(post) {
      this.selectedPost = post;
      this.dialogVisible = true;
      // 禁止背景滚动
      document.body.style.overflow = 'hidden';
    },
    closePostDetail() {
      this.dialogVisible = false;
      // 恢复背景滚动
      document.body.style.overflow = 'auto';
    },
    submitComment() {
      if (!this.commentText.trim()) {
        return;
      }
      
      if (!this.selectedPost.commentsList) {
        this.selectedPost.commentsList = [];
      }
      
      const newComment = {
        id: Date.now(),
        name: '访客',
        avatar: 'https://picsum.photos/id/1000/50/50',
        date: new Date().toLocaleDateString('zh-CN'),
        content: this.commentText.trim()
      };
      
      this.selectedPost.commentsList.push(newComment);
      this.selectedPost.comments = this.selectedPost.commentsList.length;
      this.commentText = '';
      
      // 显示成功提示
      this.$message({
        message: '评论发表成功！',
        type: 'success'
      });
    }
  }
};
</script>

<style scoped>
.blog-container {
  font-family: 'Arial', sans-serif;
}

/* 页面标题部分 */
.page-title-section {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 80px 0;
  text-align: center;
}

.page-title-section .container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

.page-title-section h1 {
  font-size: 48px;
  margin-bottom: 10px;
  font-weight: bold;
}

.page-title-section p {
  font-size: 18px;
  opacity: 0.9;
}

/* 博客内容区 */
.blog-content-section {
  padding: 60px 0;
  opacity: 0;
  transform: translateY(20px);
  transition: opacity 0.8s ease, transform 0.8s ease;
}

.blog-content-section.animate-in {
  opacity: 1;
  transform: translateY(0);
}

.blog-content-section .container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

.blog-layout {
  display: flex;
  gap: 40px;
}

.blog-main {
  flex: 1;
}

.blog-sidebar {
  flex: 0 0 300px;
}

/* 博客文章列表 */
.blog-posts {
  display: flex;
  flex-direction: column;
  gap: 30px;
}

.blog-post {
  display: flex;
  gap: 30px;
  background: white;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.05);
  cursor: pointer;
  transition: all 0.3s ease;
}

.blog-post:hover {
  transform: translateY(-5px);
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
}

.post-image {
  flex: 0 0 300px;
  overflow: hidden;
}

.post-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.5s ease;
}

.blog-post:hover .post-image img {
  transform: scale(1.05);
}

.post-content {
  flex: 1;
  padding: 30px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.post-meta {
  display: flex;
  gap: 20px;
  margin-bottom: 15px;
}

.post-date {
  font-size: 14px;
  color: #666;
}

.post-category {
  font-size: 14px;
  color: #667eea;
  font-weight: bold;
}

.post-title {
  font-size: 24px;
  color: #333;
  margin-bottom: 15px;
  font-weight: bold;
  transition: color 0.3s ease;
}

.blog-post:hover .post-title {
  color: #667eea;
}

.post-excerpt {
  font-size: 16px;
  color: #666;
  line-height: 1.6;
  margin-bottom: 20px;
}

.post-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.post-author {
  display: flex;
  align-items: center;
  gap: 10px;
}

.post-author img {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  object-fit: cover;
}

.post-author span {
  font-size: 14px;
  color: #666;
}

.post-stats {
  display: flex;
  gap: 20px;
}

.post-stats span {
  font-size: 14px;
  color: #666;
  display: flex;
  align-items: center;
  gap: 5px;
}

/* 分页 */
.pagination {
  margin-top: 40px;
  display: flex;
  justify-content: center;
}

/* 侧边栏 */
.sidebar-widget {
  background: white;
  padding: 30px;
  border-radius: 10px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.05);
  margin-bottom: 30px;
}

.sidebar-widget h3 {
  font-size: 20px;
  color: #333;
  margin-bottom: 20px;
  font-weight: bold;
  position: relative;
  padding-bottom: 10px;
}

.sidebar-widget h3::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  width: 40px;
  height: 3px;
  background: #667eea;
}

/* 搜索框 */
.search-widget .el-input {
  margin-bottom: 10px;
}

/* 分类 */
.categories-widget ul {
  list-style: none;
  padding: 0;
}

.categories-widget li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 0;
  border-bottom: 1px solid #f0f0f0;
  cursor: pointer;
  transition: all 0.3s ease;
}

.categories-widget li:hover {
  color: #667eea;
  transform: translateX(5px);
}

.categories-widget li.active {
  color: #667eea;
  font-weight: bold;
}

.category-count {
  background: #f0f0f0;
  padding: 3px 10px;
  border-radius: 10px;
  font-size: 12px;
}

/* 标签云 */
.tags-cloud {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.tag {
  display: inline-block;
  padding: 5px 15px;
  background: #f0f0f0;
  border-radius: 20px;
  font-size: 14px;
  color: #666;
  cursor: pointer;
  transition: all 0.3s ease;
}

.tag:hover {
  background: #667eea;
  color: white;
  transform: translateY(-3px);
}

.tag.active {
  background: #667eea;
  color: white;
}

/* 热门文章 */
.popular-post {
  display: flex;
  gap: 15px;
  margin-bottom: 20px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.popular-post:hover {
  transform: translateX(5px);
}

.popular-post:last-child {
  margin-bottom: 0;
}

.popular-post-image {
  flex: 0 0 80px;
  overflow: hidden;
  border-radius: 5px;
}

.popular-post-image img {
  width: 100%;
  height: 80px;
  object-fit: cover;
  transition: transform 0.5s ease;
}

.popular-post:hover .popular-post-image img {
  transform: scale(1.1);
}

.popular-post-content h4 {
  font-size: 14px;
  color: #333;
  margin-bottom: 5px;
  line-height: 1.4;
}

.popular-post-date {
  font-size: 12px;
  color: #999;
}

/* 文章详情弹窗 */
.post-detail-dialog .el-dialog__wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
}

.post-detail-dialog .el-dialog {
  max-height: 90vh;
  overflow-y: auto;
  border-radius: 10px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
}

.dialog-header {
  position: relative;
  margin-bottom: 30px;
}

.dialog-header .post-meta {
  margin-bottom: 15px;
}

.dialog-header h2 {
  font-size: 32px;
  color: #333;
  margin-bottom: 15px;
}

.dialog-header .post-author {
  display: flex;
  align-items: center;
  gap: 15px;
}

.dialog-header .post-author img {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  object-fit: cover;
}

.author-name {
  font-size: 16px;
  font-weight: bold;
  color: #333;
  margin-right: 10px;
}

.author-title {
  font-size: 14px;
  color: #666;
}

.close-button {
  position: absolute;
  top: 0;
  right: 0;
  font-size: 24px;
}

.post-detail-content {
  display: flex;
  flex-direction: column;
  gap: 30px;
}

.detail-image {
  width: 100%;
  overflow: hidden;
  border-radius: 10px;
}

.detail-image img {
  width: 100%;
  object-fit: cover;
}

.detail-text {
  font-size: 16px;
  color: #333;
  line-height: 1.8;
}

.detail-text h3 {
  font-size: 24px;
  color: #333;
  margin: 30px 0 20px 0;
}

.detail-text p {
  margin-bottom: 20px;
}

.detail-text ul {
  margin-bottom: 20px;
  padding-left: 20px;
}

.detail-text li {
  margin-bottom: 10px;
}

.detail-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin-top: 20px;
}

.detail-tags .tag {
  background: #667eea;
  color: white;
}

/* 评论区 */
.comments-section {
  margin-top: 40px;
  padding-top: 40px;
  border-top: 1px solid #e0e0e0;
}

.comments-section h3 {
  font-size: 24px;
  color: #333;
  margin-bottom: 20px;
}

.comment-input {
  margin-bottom: 40px;
}

.comments-list {
  display: flex;
  flex-direction: column;
  gap: 30px;
}

.comment-item {
  display: flex;
  gap: 15px;
}

.comment-avatar {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  object-fit: cover;
  flex-shrink: 0;
}

.comment-content {
  flex: 1;
}

.comment-header {
  display: flex;
  gap: 15px;
  margin-bottom: 10px;
}

.comment-author {
  font-weight: bold;
  color: #333;
}

.comment-date {
  font-size: 14px;
  color: #999;
}

.comment-text {
  font-size: 16px;
  color: #666;
  line-height: 1.6;
}

/* 响应式设计 */
@media (max-width: 992px) {
  .blog-layout {
    flex-direction: column;
  }
  
  .blog-sidebar {
    flex: 0 0 auto;
  }
  
  .blog-post {
    flex-direction: column;
  }
  
  .post-image {
    flex: 0 0 200px;
  }
  
  .dialog-header h2 {
    font-size: 24px;
  }
}

@media (max-width: 768px) {
  .page-title-section h1 {
    font-size: 36px;
  }
  
  .post-title {
    font-size: 20px;
  }
  
  .post-content {
    padding: 20px;
  }
  
  .sidebar-widget {
    padding: 20px;
  }
  
  .comments-section h3 {
    font-size: 20px;
  }
}
</style>