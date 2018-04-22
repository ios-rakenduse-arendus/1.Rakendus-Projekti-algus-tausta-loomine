# 1.Rakendus:Projekti algus & tausta loomine



### Projekti loomine & koodi kohandamine 

1. Ava **Xcode** ning tee uus projekt **iOS** mängu(**Game**) jaoks.

2. Anna mängule nimi, veendu et kasutad **Swift** keelt ning **SpriteKit'i** ning salvesta see endale meelepärasesse ja meeldejäävasse kausta oma arvutis.

3. Lae siit samast repositooriumst alla mängu elementide(Assets) kaust.
	* Paiguta sealt leitavad  ikoonid(**AppIcon** kaustas) Xcode projekti sees olevasse *Assets.xcassets* kausta selleks ettenähtud kohtadesse vastavalt nimele.
	* Tõsta kõik ülejäänud pildid **Assets** kaustast *Assets.xcassets* kasuta


>Kui sa esmalt teed *SpriteKit*’iga projecti annab Xcode ette üldise malli koos mõningate koodiridadega, mis võivad sulle kasuks tulla mängu loomisel. 
Selleks, et luua parem arusaamine koodis alustame selles õppematerjalis praktiliselt algusest. 
>
4. Seega vali vasakult mängu menüüst *GameScene.swift* fail ning kustutame sealt ära kõik meile hetkel ebavajaliku koodi nii, et alles oleks ainult järgnev:

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

5. Nüüd ava *GameScene.swift*'i alt *GameViewController.swift* fail ning kustuta ka sealt ülelüügne nii et alles oleks järgnev:

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

6. Järgmiseks vali Xcode menüüst ülevalt **File->New->File** ning vali sealt **Swift File** mall ja pane selle nimeks **GameElements**. Kustuta see, mis sinna automaatselt antud ning kirjuta asemele:

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
>Sellega me seadsime üles klassid füüsilistele kehadele, mida me hiljem mängus looma hakkame. 
UInt 32 - näitab, et iga kaadris olevat füüsilist keha saab määrata kuni 32 erinevasse kategooriasse, millest igaüks vastab bitt’ile bit’i maski sees.
>
>Sellega et need kategooriad on nüüd ülesse seatud saab hiljem märkida mis füüsilised kehad milliste teiste füüsiliste kehadega vastastikku mõjus on ning teineteist mõjutavad ning millal mängu teavitatakse nendest sündmustest.


7. Vali vasakult menüüst jälle *GameViewController.swift* ning otsi üles ```super.viewDidLoad()``` ning kirjuta selle alla järgmise koodi :

```swift
let scene = GameScene(size: view.bounds.size)
// VAATE SEADISTAMINE
let skView = view as! SKView
skView.showsFPS = false
skView.showsNodeCount = false

/*Pannes ignoreSiblingOrder väärtuseks false laseb see sul panna objekti sellisesse järjekorda nagu sa ise tahad , selle asemel, et süsteem teeks seda sinu eest automaatselt ja suvalisises järjekorras. */

skView.ignoresSiblingOrder = false

/* Seadista scaleMode(paneb paike mõõdistused sellest kuidas pilt ekraanil kuvatakse). .resizeFill tagab selle, et kuvatava pildi mõõtmeid muudetakse automaatselt vastavalt nii, et selle dimensioonid vastaksid alati vaate dimensioonidega. */

scene.scaleMode = .resizeFill
skView.presentScene(scene)
```
>Antud kood esindab GameScenes olevat objekti suurust nii et see vastaks sobivale raamistikule ennem kui see kuvatakse ekraanil.

---
##### *GameViewController* faili enam seda äppi tehes puutuma ei pea.
---

8. Nüüd tee lahti *GameScene.swift* fail:
	
    * Esmalt täiendame väljakutse rida nii, et see näeks välja selline: ```class GameScene: SKScene , SKPhysicsContactDelegate {```
    * Teiseks lisame ```GameScene:``` sisse meile antud mängu loomiseks vajalikud muutujad:
``` 
/* Mängu alguse ja kaotamise näitamiseks */
var isGameStarted = Bool(false)
var isDied = Bool(false)
/* Skoori salvestamiseks */
var score = Int(0)
/* Hetke skoori & parima skoori kuvamiseks */
var scoreLbl = SKLabelNode()
var highscoreLbl = SKLabelNode()
/* taptoplay teksti kuvamiseks */
var taptoplayLbl = SKLabelNode()
/* Restart & pausi nupu näitamiseks */
var restartBtn = SKSpriteNode()
var pauseBtn = SKSpriteNode()
/* Logo ja eksmati näitamiseks */
var logoImg = SKSpriteNode()
var eksmati = SKSpriteNode()
/* Seinapaari näitamiseks */
var wallPair = SKNode()

var moveAndRemove = SKAction()
```

9. Loome samasse ```GameScene:``` funktsiooni sisse, muutujate alla, uue funktsiooni nimega ```createScene``` ning lisame sinna antud koodi:
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

>Siin võtame kasutusse ka ennem loodud uues failis määratud **CollisionBitMask** klassid millega me ühendame *CollisionBitMask*’i *groundBitMask*’iga ning samaaegselt on *collisionBitMask* ning *contactTestBitMask* ühenduses *eksmatiCategory* klassiga, mis peaks tagama meie mängule teadlikuse, kui miski peaks puutuma kokku Eksmat tegelasega.
>
>```categoryBitMask:``` mask, mis defineerib selle, millistesse kategooriatesse antud füüsiline keha kuulub
>
>```collisionBitMask:``` mask mis defineerib selle, millised füüsiliste kehade kategooriad võivad antud füüsilise kehaga kokku puutuda
>
>```Collision``` ennetab objektide lõikumise, mis on algselt vaikimisi seisundiks siis kui füüsilised kehad lisatakse.
>
>```Contact``` kasutatakse, kui on vaja teada kahe objekti puutumisest, et muuta mängu käiku.



10. Nüüd selleks, et saada ekraanile taust on vaja samasse ```createScene``` funktsiooni eelneva koodi taha kirjutada järmine riba:

```swift
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

>Antud kood kuvab kaks taustapilti üksteise kõrval nii, et tekiks lõpmatult edasi liikuva tausta efekt e. kaks pilti liiguvad lõpmatuseni vasakule ning kohe kui üks neist jõuuab lõppu kuvatakse koheselt ette teine jne. Mis annab illusiooni liikuvast taustast.
>
>Taustale on antud eraldi nimi sest paljudel sprite’del võivad olla samad nimed ning eraldi nime väärtuse määramine annab võimaluse kutsuda esile parajasti soovitud elemendid.

11. Asenda ```update``` function, mis asub ```GameScene``` sees järgneva koodiga:

```swift
override func update(_ currentTime: TimeInterval) {
        // Called before each frame is rendered
        if gameStarted == true{
            if died == false{
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

>Update funktsioon kutsutakse esile ennem iga kaadri kuvamist ekraanil, mis tähendab, et kui mäng jookseb 20fps peal siis kutsutakse funktsiooni esile 20 korda. Hetkel antud koodiga me kutsume esile funktsiooni igakord liigutama taustapilti 2 piksli võrra vasakule. Taust liigub ainult pärast seda, kui isGameStarted ja isDied muutujad on kontrollitud.

12. Viimasena lisa ```didMove``` funktsiooni sisse ```createScene()```:
```swift
override func didMove(to view: SKView) {
      createScene()
    }
```

13. PLAY:
	* Nüüd kontrolli vasakult ülevalt, et projekti nime kõrval oleks valitud mingi seade(iPad, iPhone) 
	* Vajuta Xcode'i üleval vasakus nurgas olevat Play nuppu. 
>Kui kood on siiani korras peaks see käima lükkama **Simulator** akna, kus saad oma senist tööd näha jäljendatuna valitud ekraanil. Hetkel peab see kuvama ainult taustapilti.
