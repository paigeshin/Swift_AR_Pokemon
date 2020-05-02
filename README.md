# Swift_AR_Pokemon

### Basic

- node는 화면에 존재하는 object들을 의미. Hierarchy를 가질 수 있음
- material은 꾸며줌
- anchor는 어떤 object가 detected된 것을 의미함
- 보통 패턴이 SCN 시리즈로 불리우는 미리 만들어진 object를 material로 꾸며주고 node에 추가함.

### Resource Link

- [https://limitlesstcg.com/](https://limitlesstcg.com/)   ⇒ Proxy Card, AR을 기기가 인식할 수 있도록 print물 만드는 서비스
- [https://free3d.com/3d-models/oddish](https://free3d.com/3d-models/oddish)   ⇒ 3D Model 가져옴

### Swift ARKit - SceneKit

- `Assets.xcassets` 에서 AR Object Group을 생성할 수 있는데 반드시 크기 값을 지정해줘야한다.
- `Assets.xcassets` 는 2D Object를 관리함
- `art.scnassets` 는 3D asset을 가지고 관리한다

### Swift AR Concept

- anchor: The things that are detected

### mac xcode terminal로 이미지 converting 하기

- [https://free3d.com/3d-models/oddish](https://free3d.com/3d-models/oddish)
- 위 사이트에서 다운 받은 파일을 converting

```bash
#`는 빼야함 그냥 변수같은 느낌으로 적은 거임
xcrun sudz_converter `link` `Desktop/downloads` 
```

### AR Entire code

```swift
//
//  ViewController.swift
//  Poketmon3D
//
//  Created by shin seunghyun on 2020/05/03.
//  Copyright © 2020 shin seunghyun. All rights reserved.
//

import UIKit
import SceneKit
import ARKit

/***
 
 2D image를 reference 삼아서 3D AR Object rendering하게 하기
 
 ****/

class ViewController: UIViewController, ARSCNViewDelegate {
    
    @IBOutlet var sceneView: ARSCNView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // Set the view's delegate
        sceneView.delegate = self
        
        // Show statistics such as fps and timing information
        sceneView.showsStatistics = true
        
        /** Lighting 가능하게함 **/
        sceneView.autoenablesDefaultLighting = true
        
    }
    
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        
        /** Configuration을 ARIMageTrackingConfiguration으로 바꿔준다. **/
        let configuration = ARImageTrackingConfiguration()
        
        /** 2D 이미지 가져오기 **/
        if let imageToTrack = ARReferenceImage.referenceImages(inGroupNamed: "Pokemon Cards", bundle: Bundle.main) {
            configuration.trackingImages = imageToTrack
            configuration.maximumNumberOfTrackedImages = 2
            print("Images Successfully Added")
        }
        
        // Run the view's session
        sceneView.session.run(configuration)
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        
        // Pause the view's session
        sceneView.session.pause()
    }
    
    /** AR Object를 rendering하려면 아래 함수는 필수로 사용해야 한다. **/
    /** Anchor is the thing that is detected **/
    func renderer(_ renderer: SCNSceneRenderer, nodeFor anchor: ARAnchor) -> SCNNode? {
        
        let node = SCNNode()
        
        /** Data type check **/
        /** image만 detect하게 한다. **/
        if let imageAnchor = anchor as? ARImageAnchor {
            /** detect한 이미지로 똑같은 크기로 출력하게 만듬. **/
            let plane = SCNPlane(width: imageAnchor.referenceImage.physicalSize.width, height: imageAnchor.referenceImage.physicalSize.height)
            
            plane.firstMaterial?.diffuse.contents = UIColor(white: 1.0, alpha: 0.5)
            
            let planeNode = SCNNode(geometry: plane)
            
            /** Object에 딱 달라붙게 만든다. eulerAngle이 뭔지 알려고하지 말자. 걍 쓰면 된다. **/
            planeNode.eulerAngles.x = -.pi / 2
            
            node.addChildNode(planeNode)
            
            /** image anchor , reference image로 name을 가져올 수 있음. **/
            if imageAnchor.referenceImage.name ==  "eevee-card" {
                /** 3D Object Initialization, 기본적으로 Assets.scassets에 있는 3D Object를 가져옴 **/
                if let pokeScene = SCNScene(named: "art.scnassets/eeve.scn") {
                    
                    /** 카드 위에 object가 생성됨 **/
                    if let pokeNode = pokeScene.rootNode.childNodes.first {
                        pokeNode.eulerAngles.x = .pi / 2
                        planeNode.addChildNode(pokeNode)
                    }
                }
            }
            
            if imageAnchor.referenceImage.name == "oddish-card" {
                if let pokeScene = SCNScene(named: "art.scnassets/oddish.scn") {
                    /** 카드 위에 object가 생성됨 **/
                    if let pokeNode = pokeScene.rootNode.childNodes.first {
                        pokeNode.eulerAngles.x = .pi / 2
                        planeNode.addChildNode(pokeNode)
                    }
                }
            }
            
        }
        
        return node
    }
    
}
```
