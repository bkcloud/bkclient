bkclient
========

Setup
=====

* Resque
  * redis need to be installed and run<br/>
  * rake resque:work QUEUE=default<br/>
  * rake resque:scheduler<br/>

* SideKiq (Resque replacement)
	* redis required
	* bundle exec sidekiq
	* web view /sidekiq
	* define job/workers in folder app/workers
	* argument parsing only JSON
	* scheduled job: https://github.com/mperham/sidekiq/wiki/Scheduled-Jobs
	* cancel queue: https://github.com/mperham/sidekiq/wiki/Job-API
	* more info: https://github.com/mperham/sidekiq/wiki/_pages
