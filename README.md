# swift-url-session

import Foundation
import SwiftUI

//create a url session from the shared singleton session object
let sharedSession = URLSession.shared
sharedSession.configuration.allowsCellularAccess
//the config is already attached to the session so the properties are read only
sharedSession.configuration.allowsCellularAccess = false
sharedSession.configuration.allowsCellularAccess // stays true

// we need to create the config first before assigning it to a task
// to create a config object we access the default static property of urlSession configuration

let defaultConfig = URLSessionConfiguration.default
let ephemralConfig = URLSessionConfiguration.ephemeral
let backgroundConfig = URLSessionConfiguration.background(withIdentifier: "com.bgCongig.urlSession") // this ids the session, if the app is terminated while download are occuring, we can use the id to recreate the config and session objects assosiated with the trasfer

// now let's change the allow cellular access property
defaultConfig.allowsCellularAccess = false
defaultConfig.allowsCellularAccess

defaultConfig.allowsExpensiveNetworkAccess = true // indicates whether connections may use a network interface that the system considers expensive.
defaultConfig.allowsConstrainedNetworkAccess = true // allows connection in low data mode

// now if we create a session with the default config, it uses the new settings
let myDefaultSession = URLSession(configuration: defaultConfig)

// use url session to get live data from itunes
let session = URLSession(configuration: .default)

guard let url = URL(string: "https://iyunes.apple.com/search?media=music&entity=song&term=starlight") else {
    fatalError("Can't create url")
}

Task {
    let (data, response) = try await session.data(from: url)
    guard let httpResponse = response as? HTTPURLResponse,
          (200..<300).contains(httpResponse.statusCode),
          let dataString = String(data: data, encoding: .utf8) else { return }

    print(dataString)
}
