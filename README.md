# spdb
数据
from django.shortcuts import render,redirect,reverse
from django.http import HttpResponse
from .form import *
from django.contrib.auth.hashers import make_password,check_password
from .models import Userdb,Blogdb
# Create your views here.

def login(request):
    if request.method == 'GET':
        form = LoginForm()
        return render(request,'spblog/login.html',context={'form':form})
    elif request.method == 'POST':
        form = LoginForm(request.POST)
        if form.is_valid():
            username = form.cleaned_data.get('username')
            password = form.cleaned_data.get('password')
            user = Userdb.objects.filter(username=username)
            if user:
                # if check_password(password,user[0].password):
                if password == user[0].password:
                    request.session['username'] = username
                    return redirect(reverse('blog_home'))
                else:
                    return render(request,'spblog/login.html',{'error':form.errors})
            else:
                return redirect(reverse('blog_register'))
        else:
            return redirect(reverse('blog_login'))

def register(request):
    if request.method == 'GET':
        form = RegisterForm()
        return render(request,'spblog/register.html',context={'form':form})
    elif request.method == 'POST':
        form = RegisterForm(request.POST)
        if form.is_valid():
            username = form.cleaned_data.get('username')
            password = form.cleaned_data.get('password')
            password_repeat = form.cleaned_data.get('password_repeat')
            email = form.cleaned_data.get('email')
            if password == password_repeat:
                # password = make_password(password)
                Userdb.objects.create(username=username,password=password,email=email)
                return redirect(reverse('blog_login'))
            else:
                return redirect(reverse('blog_register'))
        else:
            return redirect(reverse('blog_register'))

def home(request):
    user = request.session.get('username')
    return  render(request,'spblog/blog_home.html',{'user':user})

def logout(request):
    request.session.flush()
    return redirect(reverse('blog_login'))

def blog_index(request):
    user = request.session.get('username')
    return render(request,'spblog/blog_index.html',{'user':user})

def blog_list(request):
    b_list = Blogdb.objects.all()
    return render(request,'spblog/blog_list.html',{'blog_list':b_list})

def blog_add(request):
    if request.method == 'GET':
        return render(request,'spblog/blog_add.html')
    elif request.method == 'POST':
        title = request.POST.get('title')
        content = request.POST.get('content')
        Blogdb.objects.create(title=title,content=content)
        return redirect(reverse('blog_list'))

def blog_update(request):
    pass

def blog_delete(request):
    pass
