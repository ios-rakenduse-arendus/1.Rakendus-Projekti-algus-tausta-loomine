# 4.1. Rakendus: Projekti algus ja tausta loomine

### 1. Ava **Xcode** ning tee uus projekt **iOS** mängu (**Game**) jaoks.

### 2. Anna mängule nimi, veendu et kasutad **Swift** keelt ning **SpriteKit'i** ning salvesta see endale meelepärasesse ja meeldejäävasse kausta oma arvutis.

### 3. Kohandamine:
* Eemalda seadme keeramise funktsiooni linnuksed antud äpis
* Kontrolli, Xcode menüüst *Preference*'i alt et kõik oleks ajakohane ja lae alla uuendused kui on vaja
* Soovikorral kohanda Xcode enda soovidele vastavalt


**VIDEO (1.,2.,3.):**

<a href="https://www.youtube.com/watch?v=9xajtan4anU&feature=youtu.be
" target="_blank"><img src="http://img.youtube.com/vi/9xajtan4anU/0.jpg" 
alt="Projekti loomine" width="240" height="180" border="10" /></a>


### 4. Lae siit samast repositooriumist alla mängu elementide (Assets) kaust.
* Paiguta sealt leitavad  ikoonid (**AppIcon** kaustas) Xcode projekti sees olevasse *Assets.xcassets* kausta selleks ettenähtud kohtadesse vastavalt nimele.
* Tõsta kõik ülejäänud pildid **Assets** kaustast *Assets.xcassets* kasuta

**VIDEO (4.):**

<a href="https://www.youtube.com/watch?v=ZDi18hVr7zs&feature=youtu.be
" target="_blank"><img src="http://img.youtube.com/vi/ZDi18hVr7zs/0.jpg" 
alt="Projekti loomine" width="240" height="180" border="10" /></a>


>Kui sa esmalt teed *SpriteKit*’iga projecti annab Xcode ette üldise malli koos mõningate koodiridadega, mis võivad sulle kasuks tulla mängu loomisel. 
Selleks, et luua parem arusaamine koodis alustame selles õppematerjalis praktiliselt algusest. 
>
### 5. Seega vali vasakult mängu menüüst *GameScene.swift* fail ning kustutame sealt ära kõik meile hetkel ebavajaliku koodi nii, et alles oleks ainult järgnev:

```swift
import SpriteKit
 
class GameScene: SKScene {
    
    override func didMove(to view: SKView) {
        
    }
    
    override func touchesBegan(_ touches: Set, with event: UIEvent?) {
    
    }
    
    override func update(_ currentTime: TimeInterval) {
        // Called before each frame is rendered
    }
}

```

### 6. Nüüd ava *GameScene.swift*'i alt *GameViewController.swift* fail ning kustuta ka sealt üleliigne nii, et alles oleks järgnev:

```swift
import UIKit
import SpriteKit

class GameViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        
    }
   override var shouldAutorotate: Bool {
        return false
    }
    override var supportedInterfaceOrientations: UIInterfaceOrientationMask {
        if UIDevice.current.userInterfaceIdiom == .phone {
            return .allButUpsideDown
        } else {
            return .all
        }
    }
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Release any cached data, images, etc that aren't in use.
    }
    override var prefersStatusBarHidden: Bool {
        return true
    }
}
```

### 7. Järgmiseks loo eksmati projekti kausta uus **Swift File** ja pane selle nimeks **GameElements**. Kustuta see, mis sinna automaatselt antud ning kirjutame asemele klassid füüsilistele kehadele:

```swift
import SpriteKit
 
struct CollisionBitMask {
    static let eksmatiCategory:UInt32 = 0x1 << 0
    static let postCategory:UInt32 = 0x1 << 1
    static let EAPCategory:UInt32 = 0x1 << 2
    static let groundCategory:UInt32 = 0x1 << 3
}
 
extension GameScene {
```

> UInt 32 - näitab, et iga kaadris olevat füüsilist keha saab määrata kuni 32 erinevasse kategooriasse, millest igaüks vastab bitt’ile bit’i maski sees.

**VIDEO (5.,6.,7.):**

<a href="https://www.youtube.com/watch?v=K67NO-91ce0
" target="_blank"><img src="http://img.youtube.com/vi/K67NO-91ce0/0.jpg" 
alt="Projekti loomine" width="240" height="180" border="10" /></a>

### 8. Vali vasakult menüüst jälle *GameViewController.swift* ning otsi üles ```super.viewDidLoad()``` ning kirjuta selle alla järgmise koodi :

```swift
let scene = GameScene(size: view.bounds.size)
// VAATE SEADISTAMINE
let skView = view as! SKView
skView.showsFPS = false
skView.showsNodeCount = false

skView.ignoresSiblingOrder = false

scene.scaleMode = .resizeFill
skView.presentScene(scene)
```
>Antud kood esindab GameScenes olevat objekti suurust nii, et see vastaks sobivale raamistikule ennem kui see kuvatakse ekraanil.

**VIDEO (8.):**

<a href="https://www.youtube.com/watch?v=zJ3PmHNTwGQ
" target="_blank"><img src="http://img.youtube.com/vi/zJ3PmHNTwGQ/0.jpg" 
alt="Projekti loomine" width="240" height="180" border="10" /></a>

---
##### *GameViewController* faili enam seda äppi tehes puutuma ei pea.
---

### 9. Nüüd tee lahti *GameScene.swift* fail:
	
* Esmalt täiendame väljakutse rida nii, et lisame ```SKScene``` kõrvale komaga eraldades ```SKPhysicsContactDelegate```
* Teiseks lisame ```GameScene:``` sisse meile antud mängu loomiseks vajalikud muutujad:
``` 
/* Mängu alguse ja kaotamise näitamiseks */
var isGameStarted = Bool(false)
var isDied = Bool(false)
/* Skoori salvestamiseks */
var score = Int(0)
/* Hetke skoori & parima skoori kuvamiseks */
var scoreLbl = SKLabelNode()

/* taptoplay teksti kuvamiseks */
var taptoplayLbl = SKLabelNode()
/* Restart & pausi nupu näitamiseks */
var restartBtn = SKSpriteNode()

/* Logo ja eksmati näitamiseks */
var logoImg = SKSpriteNode()

/* Seinapaari näitamiseks */
var wallPair = SKNode()

var moveAndRemove = SKAction()
```

**VIDEO (9.):**

<a href="https://www.youtube.com/watch?v=Wk5ZzLgmnm0
" target="_blank"><img src="http://img.youtube.com/vi/Wk5ZzLgmnm0/0.jpg" 
alt="Projekti loomine" width="240" height="180" border="10" /></a>

### 10. Loome samasse ```GameScene:``` klassi sisse, muutujate alla, uue funktsiooni nimega ```createScene``` ning kirjutame sinna antud koodi:
```swift
func createScene(){
/*Loome füüsilise keha terve ekraani ümber kasutades selleks edgeLoopFrom'i ning anname sellele väärtuse self.frame*/
        self.physicsBody = SKPhysicsBody(edgeLoopFrom: self.frame)
        self.physicsBody?.categoryBitMask = CollisionBitMask.groundCategory
        self.physicsBody?.collisionBitMask = CollisionBitMask.eksmatiCategory
        self.physicsBody?.contactTestBitMask = CollisionBitMask.eksmatiCategory
        self.physicsBody?.isDynamic = false
/*Pannes affectedByGravity väärtuseks false tagab selle et mängija(Eksmati) ei kukuks ekraanist välja.*/
        self.physicsBody?.affectedByGravity = false
/*Kuna mängu raskus seisneb selles et Eksmati hakkab seinte vastu minema siis selleks, et mäng tuvastaks kokkupõrkeid on vaja contactDelegate panna võrduma self’iga.*/
        self.physicsWorld.contactDelegate = self
        
        self.backgroundColor = SKColor(red: 80.0/255.0, green: 192.0/255.0, blue: 203.0/255.0, alpha: 1.0)
        
        
}
```

>```categoryBitMask:``` mask, mis defineerib selle, millistesse kategooriatesse antud füüsiline keha kuulub
>
>```collisionBitMask:``` mask mis defineerib selle, millised füüsiliste kehade kategooriad võivad antud füüsilise kehaga kokku puutuda
>
>```Collision``` ennetab objektide lõikumise, mis on algselt vaikimisi seisundiks siis kui füüsilised kehad lisatakse.
>
>```Contact``` kasutatakse, kui on vaja teada kahe objekti puutumisest, et muuta mängu käiku.



### 11. Nüüd selleks, et saada ekraanile taust on vaja samasse ```createScene``` funktsiooni eelneva koodi taha kirjutada järmine riba:

```swift

/* Liikuva tausta illusiooon */
for i in 0..<2
{
            let background = SKSpriteNode(imageNamed: "taust")
            background.anchorPoint = CGPoint.init(x: 0, y: 0)
            background.position = CGPoint(x:CGFloat(i) * self.frame.width, y:0)
            background.name = "background"
            background.size = (self.view?.bounds.size)!
            self.addChild(background)
}
```

### 12. Asenda ```update``` function, mis asub ```GameScene``` sees järgneva koodiga:

```swift
override func update(_ currentTime: TimeInterval) {
        // Called before each frame is rendered
        if isGameStarted == true{
            if isDied == false{
                enumerateChildNodes(withName: "background", using: ({
                    (node, error) in
                    let taust = node as! SKSpriteNode
                    taust.position = CGPoint(x: taust.position.x - 2, y: taust.position.y)
                    if taust.position.x <= -taust.size.width {
                        taust.position = CGPoint(x:taust.position.x + taust.size.width * 2, y:taust.position.y)
                    }
                }))
            }
        }
    }
}
```

### 13. Viimasena lisa ```didMove``` funktsiooni sisse ```createScene()```:
```swift
override func didMove(to view: SKView) {
      createScene()
    }
```

### 14. PLAY:
* Nüüd kontrolli vasakult ülevalt, et projekti nime kõrval oleks valitud mingi seade (iPad, iPhone) 
* Vajuta Xcode'i üleval vasakus nurgas olevat Play nuppu. 
>Kui kood on siiani korras peaks see käima lükkama **Simulator** akna, kus saad oma senist tööd näha jäljendatuna valitud ekraanil. Hetkel peab see kuvama ainult taustapilti.

**VIDEO (10.,11.,12.,13.,14.):**

<a href="https://www.youtube.com/watch?v=iCQ4IL5l1cI
" target="_blank"><img src="http://img.youtube.com/vi/iCQ4IL5l1cI/0.jpg" 
alt="Projekti loomine" width="240" height="180" border="10" /></a>

___
**Tubli! Algus mängurakenduse loomiseks on tehtud. Täida nüüd siin all olevad ülesanded.**
__________

# ÜLESANNE:

* Lisa muutujatesse selleks ettenähtud kohta highscoreLbl (parim skoor tekst) samamoodi nagu panime kirja skoori
* Lisa muutujatesse selleks ettenähtud kohta pauseBtn (pausi nupp) samamoodi nagu eelnevalt panime kirja restart
* Lisa muutjuatesse selleks ettenähtud kohta eksmati samamoodi nagu eelnevalt tegime muutuja logoImg jaoks

* Teadmiste kontrolliks täida ülesanne *[Learningapps](https://learningapps.org/watch?v=pihhevmit18)* lehel. 
