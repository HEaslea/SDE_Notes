Providing a simplified interface to a complex subsystem. 

Often for making a library or API much easier. 

eg. a home theatre: 

First we need a series of subsystems, systems that the client will use indirectly through the facade. 

There is the idea that classes encapsulate state and operations. 
Subsystems encapsulate classes and their dependencies. 

There can be a convoluted system of dependencies within subsystems, we don't want our clients to get bogged down in that, therefore we make the client facades. 

Say that within our theatre system , we already have our subsystems. 

```
#include <string> 
#include "DVDPlayer.h"
#include "Projector.h"
#include "SoundSystem.h"

class HomeTheaterFacade { 
public: 
	HomeTheaterFacade(DVDPlayer dvd, Projector* projector, 
		SoundSystem* sound) : dvdPlayer(dvd), projector(projector),
				soundSystem(sound) {}


	void watchMovie(const std::string& movie)
	{ 
		// turning our subsystems on through their interface
		dvdPlayer->stop();
		// ... 
	}


private: 
	DVDPlayer* dvdPlayer;
	Projector* projector;
	//...
};
```

```
// main
// with all the includes that we could need

int main(){
	// then the obvious
	DVDPlayer dvdPlayer;
	//... etc. 
	a
	HomeTheaterFacade homeTheater(&dvdPlayer, ...);

	HomeThater.watchMove("Movie Title");
	return 0;
}
```

These are just preliminary, just to give an idea of the design. 

