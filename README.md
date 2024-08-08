## Creating a Blog Page with React and Django

This guide outlines the steps to build a basic blog page using React for the frontend and Django for the backend API.

**Assumptions:**

* You have basic knowledge of React and Django.
* You have Node.js and npm/yarn installed.
* You have Python and pip installed.

### 1. Setting up the Django Backend:

**1.1 Create Django Project:**

```bash
django-admin startproject blog_project
```

**1.2 Create Django App:**

Navigate to the project directory:

```bash
cd blog_project
```

Create the blog app:

```bash
python manage.py startapp blog
```

**1.3 Define Models:**

**blog_project/blog/models.py:**

```python
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    pub_date = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

**1.4 Migrate Database:**

```bash
python manage.py makemigrations
python manage.py migrate
```

**1.5 Create Serializer:**

**blog_project/blog/serializers.py:**

```python
from rest_framework import serializers
from .models import Post

class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = '__all__'
```

**1.6 Create API View:**

**blog_project/blog/views.py:**

```python
from rest_framework import viewsets
from .models import Post
from .serializers import PostSerializer

class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
```

**1.7 Register Viewset with Router:**

**blog_project/blog_project/urls.py:**

```python
from django.contrib import admin
from django.urls import path, include
from rest_framework import routers
from blog import views

router = routers.DefaultRouter()
router.register(r'posts', views.PostViewSet)

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include(router.urls)),
]
```

**1.8 Start Development Server:**

```bash
python manage.py runserver
```

The backend is now set up. You should be able to access the API at `http://localhost:8000/api/posts/`.

### 2. Creating the React Frontend:

**2.1 Create React App:**

```bash
npx create-react-app blog-frontend
```

**2.2 Navigate to Frontend Directory:**

```bash
cd blog-frontend
```

**2.3 Install Axios (for API calls):**

```bash
npm install axios
```

**2.4 Create Components:**

**blog-frontend/src/components/PostList.js:**

```javascript
import React, { useState, useEffect } from 'react';
import axios from 'axios';

const PostList = () => {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    const fetchPosts = async () => {
      const res = await axios.get('http://localhost:8000/api/posts/');
      setPosts(res.data);
    };
    fetchPosts();
  }, []);

  return (
    <div>
      <h1>Blog Posts</h1>
      <ul>
        {posts.map(post => (
          <li key={post.id}>
            <h2>{post.title}</h2>
            <p>{post.content}</p>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default PostList;
```

**blog-frontend/src/App.js:**

```javascript
import React from 'react';
import PostList from './components/PostList';

function App() {
  return (
    <div className="App">
      <PostList />
    </div>
  );
}

export default App;
```

**2.5 Run React Development Server:**

```bash
npm start
```

This launches your React application on `http://localhost:3000/`. You should now see the blog posts fetched from your Django API.

### 3. Expanding Functionality:

This sets up a basic foundation. You can expand upon this by:

* **Adding Post Creation:** Create a