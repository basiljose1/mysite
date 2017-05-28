# mysite
Django sample
python -m django --version

django-admin startproject mysite


python manage.py startapp polls


DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'data_name',
        'USER': 'root',
        'PASSWORD': 'root',
        'HOST': '',
        'PORT': '',
    }
}

class RequestTypeMiddleware:
    def process_request(self, request):
        """Identify the request type - Normal site or widget"""
        institution_id = None
        host_url = request.META['HTTP_HOST']
        if InstitutionProfile.objects.filter(host_url=host_url):
            institution_id = InstitutionProfile.objects.filter(host_url=host_url)[0].id
        request.session['institution_id'] = institution_id
        request.session['site_url'] = host_url
        request.session['byj_site'] = byj_site
        return
        
 
class ProtectedLoginView(TemplateView):
    template_name = ''

    @method_decorator(login_required(login_url='/'))
    def dispatch(self, *args, **kwargs):
        return super(ProtectedLoginView, self).dispatch(*args, **kwargs)

from functools import wraps

from django.contrib.auth.decorators import login_required

from django.utils.decorators import method_decorator


def requires_candidate_login(f):
    """ Decorator for candidate logins """
    @wraps(f)
    def decorated(request, *args, **kwargs):
        if request.user.profile.account_type != 0:
            return render(request, 'access_check.html')
        return f(request, *args, **kwargs)
    return decorated
    
    
    
context proceccssors


def get_institution(request):
    institution_id = request.session.get('institution_id', None)
    context = {'institution_id': institution_id}
    context['SITE_URL'] = request.META['HTTP_HOST']
    if institution_id:
        institution = InstitutionProfile.objects.get(id=institution_id)
        context['institution_name'] = institution.name
        context['institution_logo'] = institution.user.profile.avatar.name
    return context
