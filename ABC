local Players = game:GetService("Players")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local GuiService = game:GetService("GuiService")
local CoreGui = game:GetService("CoreGui")
local StarterGui = game:GetService("StarterGui")
 
-- Create UI
local screenGui = Instance.new("ScreenGui")
local frame = Instance.new("Frame")
local userIdTextbox = Instance.new("TextBox")
local placeIdTextbox = Instance.new("TextBox")
local playerCountTextbox = Instance.new("TextBox")
local startButton = Instance.new("TextButton")
local closeButton = Instance.new("TextButton")
local label = Instance.new("TextLabel")
 
-- Configure UI elements
frame.BackgroundColor3 = Color3.new(0.15, 0.15, 0.15)
closeButton.BackgroundColor3 = Color3.new(0.25, 0.25, 0.25)
label.BackgroundColor3 = Color3.new(0.25, 0.25, 0.25)
startButton.BackgroundColor3 = Color3.new(0.25, 0.25, 0.25)
userIdTextbox.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
placeIdTextbox.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
playerCountTextbox.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
 
closeButton.TextColor3 = Color3.new(1, 1, 1)
label.TextColor3 = Color3.new(1, 1, 1)
startButton.TextColor3 = Color3.new(1, 1, 1)
userIdTextbox.TextColor3 = Color3.new(1, 1, 1)
placeIdTextbox.TextColor3 = Color3.new(1, 1, 1)
playerCountTextbox.TextColor3 = Color3.new(1, 1, 1)
 
closeButton.BorderSizePixel = 0
label.BorderSizePixel = 0
startButton.BorderSizePixel = 0
userIdTextbox.BorderSizePixel = 0
placeIdTextbox.BorderSizePixel = 0
playerCountTextbox.BorderSizePixel = 0
 
-- Set default values and parent elements
frame.Size = UDim2.new(0, 300, 0, 290)
frame.Position = UDim2.new(0.5, -150, 0.5, -145)
frame.Parent = screenGui
 
closeButton.Text = "Close"
closeButton.Size = UDim2.new(0, 75, 0, 40)
closeButton.Position = UDim2.new(1, -75, 0, 0)
closeButton.Parent = frame
 
label.Text = "Stream Sniper Reborn"
label.Size = UDim2.new(0, 225, 0, 40)
label.Position = UDim2.new(0, 0, 0, 0)
label.Parent = frame
 
userIdTextbox.Text = ""
userIdTextbox.Size = UDim2.new(0, 280, 0, 50)
userIdTextbox.Position = UDim2.new(0, 10, 0, 50)
userIdTextbox.PlaceholderText = "User Id"
userIdTextbox.Parent = frame
 
placeIdTextbox.Text = tostring(game.PlaceId) -- Default PlaceId
placeIdTextbox.Size = UDim2.new(0, 280, 0, 50)
placeIdTextbox.Position = UDim2.new(0, 10, 0, 110)
placeIdTextbox.PlaceholderText = "Place Id"
placeIdTextbox.Parent = frame
 
playerCountTextbox.Text = "1"
playerCountTextbox.Size = UDim2.new(0, 280, 0, 50)
playerCountTextbox.Position = UDim2.new(0, 10, 0, 170)
playerCountTextbox.PlaceholderText = "Minimum Player Count"
playerCountTextbox.Parent = frame
 
startButton.Text = "Start"
startButton.Size = UDim2.new(0, 280, 0, 50)
startButton.Position = UDim2.new(0, 10, 0, 230)
startButton.Parent = frame
 
screenGui.Parent = CoreGui
 
-- Functions
 
local function status(text)
    startButton.Text = text
end
 
local function notify(title, text)
    StarterGui:SetCore("SendNotification", {
        Title = title,
        Text = text,
        Duration = 3
    })
end
 
-- Http Requests
local reqf
if syn and syn.request then
   reqf = syn.request
elseif request then
   reqf = request
elseif http and http.request then
   reqf = http.request
elseif http_request then
   reqf = http_request
else
    notify("Error", "Your executor does not support http requests")
end
 
local function fetchJson(url)
    return HttpService:JSONDecode(reqf({Url = url}).Body)
end
 
-- Function to get user avatar by UserId
local function getUserAvatarByUserId(userId)
    local url = "https://thumbnails.roblox.com/v1/users/avatar-headshot?userIds="..userId.."&size=48x48&format=Png&isCircular=false"
    local response = reqf({Url = url}).Body
    return HttpService:JSONDecode(response).data[1].imageUrl
end
 
-- Function to get user avatars by PlayerTokens
local function getUserAvatarsByTokens(playerTokens)
    local url = "https://thumbnails.roblox.com/v1/batch"
    local data = {}
    for _, token in ipairs(playerTokens) do
        table.insert(data, {
            token = token,
            type = "AvatarHeadShot",
            size = "48x48",
            isCircular = false
        })
    end
    data = HttpService:JSONEncode(data)
    local headers = {
        ["Content-Type"] = "application/json"
    }
    local response = reqf({
        Url = url,
        Method = "POST",
        Headers = headers,
        Body = data
    }).Body
    local imageUrls = {}
    for _, item in ipairs(HttpService:JSONDecode(response).data) do
        table.insert(imageUrls, item.imageUrl)
    end
    return imageUrls
end
 
local running = false
local debounce = false
local kill = false
 
-- Main function
local function main()
    if kill then return end
    if running then 
        if debounce then
            notify("Cancelled", "Cancelled")
            kill = true
        else
            debounce = true
            notify("Confirm action", "Click again to cancel")
            wait(3)
            if running then
                debounce = false
            end
        end
        return
    else
        running = true
    end
 
    local userId = tonumber(userIdTextbox.Text)
    local placeId = tonumber(placeIdTextbox.Text)
    local minPlayerCount = tonumber(playerCountTextbox.Text)
    
    if not userId then
        status('Retrieving user id...')
        local userData = HttpService:JSONDecode(reqf({
            Url = "https://users.roblox.com/v1/usernames/users",
            Method = "POST",
            Headers = {
                ["accept"] = "application/json",
                ["Content-Type"] = "application/json"
            },
            Body = HttpService:JSONEncode({
                usernames = { userIdTextbox.Text },
                excludeBannedUsers = false
            })
        }).Body).data
        if #userData == 0 then
            notify('User does not exist!')
            return
        end
        userId = userData.id
    end
    
    status('Retrieving user info...')
    
    local userInfo = fetchJson("https://users.roblox.com/v1/users/" .. userId)
    
    if userInfo.errors and userInfo.errors[0] then
        notify(userInfo.errors[0].userFacingMessage, userInfo.errors[0].message)
        return
    end
    
    if userInfo.isBanned then
        notify("Error", userInfo.displayName .. " is banned")
    end
    
    notify("Searching for", userInfo.displayName .. " (" .. userInfo.name .. ")")
 
    status("Loading profile picture...")
    local userAvatarUrl = getUserAvatarByUserId(userId)
 
    local cursor = ""
    local found = false
 
    local page = 1
 
    repeat
        if kill then
            kill = false
            running = false
            debounce = false
            return
        end
        -- Get list of servers
        status("Retrieving server list... (page" .. page .. ")")
        local url = "https://games.roblox.com/v1/games/"..placeId.."/servers/Public?sortOrder=Asc&limit=100"
        if cursor then
            url = url .. "&cursor=" .. cursor
        end
        local response = reqf({ Url = url }).Body
        local data = HttpService:JSONDecode(response)
        for i, server in ipairs(data.data) do
            if kill then
                kill = false
                running = false
                debounce = false
                return
            end
            if server.playing < minPlayerCount then continue end
            status("Scanning servers (page " .. page .. " - " .. i .. "/" .. #data.data .. " - " .. server.playing .. " online)")
            local serverAvatarUrls = getUserAvatarsByTokens(server.playerTokens)
            for _, serverAvatarUrl in ipairs(serverAvatarUrls) do
                if serverAvatarUrl == userAvatarUrl then
                    status("Player found, teleporting...")
                    wait(0.1)
                    TeleportService:TeleportToPlaceInstance(placeId, server.id, Players.LocalPlayer)
                    found = true
                    wait(3)
                    break
                end
            end
            if found then break end
        end
 
        cursor = data.nextPageCursor or ""
        page = page + 1
    until found or cursor == ""
 
    if not found then
        notify("Error", "The player was not found on the specified place")
        running = false
    end
end
 
-- Define button click event
startButton.MouseButton1Click:Connect(function()
    local Success, Message = pcall(main)
    status('Start')
    if not Success then
        notify("Error", Message)
        screenGui:Destroy()
    end
end)
 
-- Define button click event
closeButton.MouseButton1Click:Connect(function()
    kill = true
    screenGui:Destroy()
end)
