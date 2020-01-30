# iPhone Cocos2D Game

The idea of the game is to move the green square up and down, and avoid bumping into the horizontally-moving red squares.

![](https://raw.githubusercontent.com/Ruslan-Aliyev/Cocos2D-iOS/master/Illustrations/ios1.PNG)

![](https://raw.githubusercontent.com/Ruslan-Aliyev/Cocos2D-iOS/master/Illustrations/ios2.PNG)

```
//  HelloWorldLayer.h
//  AvoidRed

#import &lt;GameKit/GameKit.h&gt;

// When you import this file, you import all the cocos2d classes
#import "cocos2d.h"

// HelloWorldLayer
@interface HelloWorldLayer : CCLayer &lt;GKAchievementViewControllerDelegate, 
	GKLeaderboardViewControllerDelegate&gt;{}

// returns a CCScene that contains the HelloWorldLayer as the only child
+(CCScene *) scene;

@end
```

```
//  HelloWorldLayer.m
//  AvoidRed

// Import the interfaces
#import "HelloWorldLayer.h"
#import "GameOver.h"

CCSprite *avoided1;
CCSprite *avoided2;
CCSprite *avoided3;
CCSprite *avoider;

CGRect avoiderRect;
CGRect avoided1Rect;
CGRect avoided2Rect;
CGRect avoided3Rect;

// Needed to obtain the Navigation Controller
#import "AppDelegate.h"

#pragma mark - HelloWorldLayer

// HelloWorldLayer implementation
@implementation HelloWorldLayer

// Helper class method that creates a Scene with the HelloWorldLayer as the only child.
+(CCScene *) scene{

	// 'scene' is an autorelease object.
	CCScene *scene = [CCScene node];

	// 'layer' is an autorelease object.
	HelloWorldLayer *layer = [HelloWorldLayer node];

	// add layer as a child to scene
	[scene addChild: layer];

	// return the scene
	return scene;
}

// on "init" you need to initialize your instance
-(id) init{

	// always call "super" init
	// Apple recommends to re-assign "self" with the "super's" return value
	if( (self=[super init]) ) {

        avoided1 =[CCSprite spriteWithFile:@"avoided.PNG"];
        avoided1.position=ccp(300, 80);
        [self addChild:avoided1];

        avoided2 =[CCSprite spriteWithFile:@"avoided.PNG"];
        avoided2.position=ccp(480, 170);
        [self addChild:avoided2];

        avoided3 =[CCSprite spriteWithFile:@"avoided.PNG"];
        avoided3.position=ccp(200, 260);
        [self addChild:avoided3];

        avoider =[CCSprite spriteWithFile:@"avoider.PNG"];
        avoider.position=ccp(200, 80);
        [self addChild:avoider];

        CCMenuItemImage *up = [CCMenuItemImage itemFromNormalImage:@"up.png" 
			selectedImage:@"up.png" target:self selector:@selector(moveUp)];

        CCMenu *menuUp = [CCMenu menuWithItems:up,nil];

        menuUp.position = ccp(150,16);

        [self addChild:menuUp];

		
        CCMenuItemImage *down = [CCMenuItemImage itemFromNormalImage:@"down.png" 
			selectedImage:@"down.png" target:self selector:@selector(moveDown)];

        CCMenu *menuDown = [CCMenu menuWithItems:down,nil];

        menuDown.position = ccp(350,16);

        [self addChild:menuDown];

        
        [self schedule:@selector(callEveryFrame:)];
	}
	return self;
}

// on "dealloc" you need to release all your retained objects
- (void) dealloc{

	// in case you have something to dealloc, do it in this method
	// in this particular example nothing needs to be released.
	// cocos2d will automatically release all the children (Label)

	// don't forget to call "super dealloc"
	[super dealloc];
}

-(CGRect)rectAvoider{

    return CGRectMake(avoider.position.x-(avoider.contentSize.width/2),
		avoider.position.y-(avoider.contentSize.height/2),
		avoider.contentSize.width,avoider.contentSize.height);
}

-(CGRect)rectAvoided1{

    return CGRectMake(avoided1.position.x-(avoided1.contentSize.width/2),
		avoided1.position.y-(avoided1.contentSize.height/2),
		avoided1.contentSize.width,avoided1.contentSize.height);
}

-(CGRect)rectAvoided2{

    return CGRectMake(avoided2.position.x-(avoided2.contentSize.width/2),
		avoided2.position.y-(avoided2.contentSize.height/2),
		avoided2.contentSize.width,avoided2.contentSize.height);
}

-(CGRect)rectAvoided3{

    return CGRectMake(avoided3.position.x-(avoided3.contentSize.width/2),
		avoided3.position.y-(avoided3.contentSize.height/2),
		avoided3.contentSize.width,avoided3.contentSize.height);
}

-(void)moveUp{

    avoider.position=ccp(avoider.position.x, avoider.position.y+20);
    if(avoider.position.y&gt;320){
        avoider.position=ccp(avoider.position.x, 320);
    }
}

-(void)moveDown{

    avoider.position=ccp(avoider.position.x, avoider.position.y-20);
    if(avoider.position.y&lt;80){
        avoider.position=ccp(avoider.position.x, 80);
    }
}

- (void) callEveryFrame:(ccTime)dt{

    avoided1.position=ccp(avoided1.position.x +100*dt, avoided1.position.y);
    if(avoided1.position.x&gt;480+160){
        avoided1.position=ccp(-160, avoided1.position.y);
    }

    avoided2.position=ccp(avoided2.position.x -200*dt, avoided2.position.y);
    if(avoided2.position.x&lt;-160){
        avoided2.position=ccp(480+160, avoided2.position.y);
    }

    avoided3.position=ccp(avoided3.position.x +200*dt, avoided3.position.y);
    if(avoided3.position.x&gt;480+160){
        avoided3.position=ccp(-160, avoided3.position.y);
    }

    avoiderRect=[self rectAvoider];
    avoided1Rect=[self rectAvoided1];
    avoided2Rect=[self rectAvoided2];
    avoided3Rect=[self rectAvoided3];

    if(CGRectIntersectsRect(avoiderRect,avoided1Rect)||
		CGRectIntersectsRect(avoiderRect,avoided2Rect)||
		CGRectIntersectsRect(avoiderRect,avoided3Rect)){

        [[CCDirector sharedDirector]replaceScene:
			[CCTransitionFade transitionWithDuration:1 scene:[GameOver node]]];
    }
}

#pragma mark GameKit delegate

-(void) achievementViewControllerDidFinish:(GKAchievementViewController *)viewController{

	AppController *app = (AppController*) [[UIApplication sharedApplication] delegate];
	[[app navController] dismissModalViewControllerAnimated:YES];
}

-(void) leaderboardViewControllerDidFinish:(GKLeaderboardViewController *)viewController{

	AppController *app = (AppController*) [[UIApplication sharedApplication] delegate];
	[[app navController] dismissModalViewControllerAnimated:YES];
}
@end
```

```
//  GameOver.m
//  AvoidRed

#import "GameOver.h"
#import "HelloWorldLayer.h"

@implementation GameOver

+(id) scene {
    CCScene *scene = [CCScene node];
    GameOver *layer = [GameOver node];
    [scene addChild: layer];
    return scene;
}

-(id) init{
    if( (self=[super init] )) {
        self.isTouchEnabled = YES;
        CCLabelTTF *label = [CCLabelTTF labelWithString:@"New Game"
                                               fontName:@"Courier New"
                                               fontSize:40];
        label.position = ccp(240, 240);
        [self addChild: label];
    }
    return self;
}

-(BOOL) ccTouchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
    [[CCDirector sharedDirector]
     replaceScene:[CCTransitionFade
                   transitionWithDuration:1
                   scene:[HelloWorldLayer node]
                   ]];
    return YES;
}
@end
```

```
//  GameOver.h
//  AvoidRed

#import &lt;Foundation/Foundation.h&gt;
#import "cocos2d.h"

@interface GameOver : CCLayer {}

+(id)scene;

@end
```
