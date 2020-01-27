# Lesson 3 - Getting the channels package installed and configured

Django channels

1. Install Django channels
2. Add channels to app in setting

[
	...
	‘channels’,
,]

1. Add channel_layers to setting
    - Channel-layers acts as a messenger between built piece of code
    - In memory layer - fine for development, but not so great for production
    - Limit data to strings, bool, number, list, and dictionary

    SYNTAX

// PROJECT_NAME/Settings.py
CHANNEL_LAYERS = {
	‘default’: {
		‘BACKEND’: 'asgiref. inmemory.ChannelLayer’,
		‘ROUTING’: ‘djangocity.routing.channel_routing’
	}
}


// PROJECT_NAME/routing.py
From channels.routing import route

channel_routing = [
]



// APP_NAME/signals.py
from django.db.models.signals import post_save
from django.dispatch import receiver

from .models import Vote


@receiver(post_save, sender=Vote)
def vote_save_handler(sender, **kwargs):
	print(“saved! )


// APP_NAME/consumers.py
From channels import Group
From .models import Vote, Voter

Def vote_saved(message):
	data = {}
	for voter in Voter.objects.all():
		blank_data = {}
		for choice in Vote.CATEGORIES:
			blank_data[choice[0]] = {‘yes’: 0, ‘no’: 0}

		for vote in Vote.objects.all():
			for voter in vote.yes_votes.all():
				data[voter.id][voter.category][‘yes’] += 1


