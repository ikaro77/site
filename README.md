from flask import Flask, render_template_string, request, redirect, url_for

app = Flask(__name__)

posts = {
    1: {"title": "사진 속 고양이", "content": "귀엽죠? 🐱", "comments": []}
}

# HTML 템플릿
template_post_detail = '''
<!DOCTYPE html>
<html>
<head>
    <title>{{ post.title }}</title>
    <style>
        body { font-family: Arial; margin: 0; padding: 0; }
        .post-container { padding: 20px; }
        .comments { margin-top: 20px; }
        .comment { padding: 5px 0; border-bottom: 1px solid #eee; }
        .comment-input { 
            position: fixed; bottom: 0; left: 0; right: 0; 
            padding: 10px; 
            border-top: 1px solid #ccc; 
            background: #fafafa;
            display: flex;
        }
        .comment-input input[type=text] {
            flex: 1;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 20px;
            outline: none;
            font-size: 14px;
        }
        .comment-input button {
            margin-left: 10px;
            padding: 10px 20px;
            border: none;
            background-color: #3897f0;
            color: white;
            border-radius: 20px;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div class="post-container">
        <h2>{{ post.title }}</h2>
        <p>{{ post.content }}</p>

        <div class="comments">
            {% for c in post.comments %}
                <div class="comment">{{ c }}</div>
            {% endfor %}
        </div>
    </div>

    <form class="comment-input" method="POST">
        <input type="text" name="comment" placeholder="댓글 달기..." required autocomplete="off">
        <button type="submit">게시</button>
    </form>
</body>
</html>
'''

@app.route('/post/<int:post_id>', methods=['GET', 'POST'])
def post_detail(post_id):
    post = posts.get(post_id)
    if not post:
        return "글을 찾을 수 없습니다.", 404
    if request.method == 'POST':
        comment = request.form['comment']
        post['comments'].append(comment)
        return redirect(url_for('post_detail', post_id=post_id))
    return render_template_string(template_post_detail, post=post)

if __name__ == '__main__':
    app.run(debug=True)
