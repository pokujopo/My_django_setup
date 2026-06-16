# My_django_setup

#kwenye project yako lazima urun hii command kabla uja weka izo setup apo chini
pip install django djangorestframework djangorestframework-simplejwt dj-rest-auth django-allauth


# urls.py
from django.contrib import admin
from django.urls import path, include
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
    path('admin/', admin.site.urls),
    
    # Login na Refresh ya JWT Moja kwa Moja kutoka kwenye Mzizi (Uhakika 100%)
    path('api/auth/login/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/auth/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
    
    # Endpoints za Password Reset, Logout na Usajili (Registration)
    path('api/auth/', include('dj_rest_auth.urls')),
    path('api/auth/registration/', include('dj_rest_auth.registration.urls')),
]




# settings.py

INSTALLED_APPS = [
    # ... apps za django za kawaida ...
    'django.contrib.sites',
    
    # Mfumo wa API na Auth
    'rest_framework',
    'rest_framework.authtoken',     # Lazima iwepo ili dj-rest-auth isilalamike isipowaka
    'rest_framework_simplejwt',     # Injini ya kutengeneza JWT
    'dj_rest_auth',                 # Dalali wa endpoints (Logout, Password Reset)
    'dj_rest_auth.registration',    # Dalali wa usajili (Signup)
    
    # Mambo ya Social Login (Kama Google)
    'allauth',
    'allauth.account',
    'allauth.socialaccount',
    'allauth.socialaccount.providers.google',
]

SITE_ID = 1

# 1. KULAZIMISHA JWT KILA SEHEMU (HAPA NDIO NYUMBANI)
REST_USE_JWT = True
REST_AUTH_TOKEN_MODEL = None  # <── DAWA YA USUGU: Inakataza katakata utengenezaji wa "key" za kizamani kwenye DB!
JWT_AUTH_HTTPONLY = False     # Inaruhusu Flutter/React kusoma token kirahisi kwenye body

# Mipangilio ya Cookies za JWT (Zinafaa hata ukitumia React web)
JWT_AUTH_COOKIE = 'my-app-auth'
JWT_AUTH_REFRESH_COOKIE = 'my-app-refresh-token'
JWT_AUTH_RETURN_EXPIRATION = True

# Serializers za kulazimisha JWT wakati wa Login na Register
REST_AUTH_SERIALIZERS = {
    'JWT_TOKEN_CLAIMS_SERIALIZER': 'rest_framework_simplejwt.serializers.TokenObtainPairSerializer',
    'LOGIN_SERIALIZER': 'dj_rest_auth.serializers.LoginSerializer',
    'REGISTER_SERIALIZER': 'dj_rest_auth.registration.serializers.RegisterSerializer',
}

# 2. SHERIA YA GLOBAL YA DRF (Milango yote inataka Bearer JWT)
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ),
}

# Middleware ya lazima ya allauth
MIDDLEWARE = [
    # ... middleware za kawaida ...
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'allauth.account.middleware.AccountMiddleware', # <── USISAHAU HII!
]

# Kuzima email verification wakati wa testing
ACCOUNT_EMAIL_VERIFICATION = 'none'
ACCOUNT_AUTHENTICATION_METHOD = 'username_email'
ACCOUNT_EMAIL_REQUIRED = True
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'

