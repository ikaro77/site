from flask import Flask, render_template_string, request, redirect, url_for

app = Flask(__name__)

# 더미 블로그 글
posts = {
    1: {"title": "첫 번째 글", "content": "이것은 첫 번째 블로그 글입니다.", "comments": []},
    2: {"title": "두 번째 글", "content": "이것은 두 번째 블로그 글입니다.", "comments": []}
}

# 템플릿 (Jinja2)
template_post_list = '''
<h2>블로그 글 목록</h2>
<ul>
    {% for pid, post in posts.items() %}
        <li><a href="{{ url_for('post_detail', post_id=pid) }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>
'''

template_post_detail = '''
<h2>{{ post.title }}</h2>
<p>{{ post.content }}</p>

<h3>댓글</h3>
<ul>
    {% for c in post.comments %}
        <li>{{ c }}</li>
    {% endfor %}
</ul>

<form method="POST">
    <input type="text" name="comment" placeholder="댓글 입력" required>
    <button type="submit">작성</button>
</form>

<p><a href="{{ url_for('post_list') }}">← 목록으로</a></p>
'''

# 라우터
@app.route('/')
def post_list():
    return render_template_string(template_post_list, posts=posts)

@app.route('/post/<int:post_id>', methods=['GET', 'POST'])
def post_detail(post_id):
    post = posts.get(post_id)
    if not post:
        return "존재하지 않는 글입니다.", 404
    if request.method == 'POST':
        comment = request.form['comment']
        post['comments'].append(comment)
        return redirect(url_for('post_detail', post_id=post_id))
    return render_template_string(template_post_detail, post=post)

if __name__ == '__main__':
    app.run(debug=True)
