--// VENDETTA HUB PREMIUM
--// Vendetta Hub Presents
--// Premium Mobile + PC Edition

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RS = game:GetService("RunService")
local TS = game:GetService("TweenService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")

local LP = Players.LocalPlayer
local PG = LP:WaitForChild("PlayerGui")

local Character = LP.Character or LP.CharacterAdded:Wait()
local HRP = Character:WaitForChild("HumanoidRootPart")
local Humanoid = Character:WaitForChild("Humanoid")

--==================================================
--// STATE
--==================================================

local HealthESP = false
local NameDistanceESP = false
local FluteESP = false
local EarringsESP = false
local NoArm = false
local AutoBlock = false
local InfiniteDash = false
local Noclip = false
local RGB = true
local Flying = false
local Farm = false
local FarmTarget = nil
local FarmTween = nil

-- Vendetta combat / utility states
local FriendIgnore = false
local HitboxESP = false
local AutoBreathing = false
local KatanaHidden = false
local TrainerTween = nil
local IceShardESP = false
local IceShardTween = nil

local TrainerPoints = {
    ["Rock Trainer"] = Vector3.new(-206.29934692382812, 825.102294921875, 1351.5655517578125),
    ["Water Trainer"] = Vector3.new(530.67431640625, 819.4022216796875, 782.8063354492188),
    ["Beast Trainer"] = Vector3.new(-46.305755615234375, 821.6522827148438, 184.3372802734375),
    ["Thunder Trainer"] = Vector3.new(-2012.3070068359375, 830.0028686523438, -1037.0313720703125),
    ["Serpent Trainer"] = Vector3.new(-2557.933837890625, 830.0028076171875, -490.3348693847656),
    ["Flame Trainer"] = Vector3.new(-2390.70556640625, 827.4526977539062, 548.4616088867188),
    ["Flower Trainer"] = Vector3.new(-1257.1767578125, 868.301513671875, 1343.238525390625),
    ["Wind Trainer"] = Vector3.new(-1172.930908203125, 868.301513671875, 1336.7552490234375),
    ["Rat Trainer"] = Vector3.new(-1304.7491455078125, 1059.801513671875, 791.6906127929688),
    ["Love Trainer"] = Vector3.new(-1159.5474853515625, 885.208740234375, 849.25537109375),
}

local FlySpeed = 50
local Vertical = 0
local UpHeld = false
local DownHeld = false

local ESPObjects = {}
local NoclipConnection = nil
local FarmConnection = nil

--==================================================
--// HELPERS
--==================================================

local function GetCharacter(P)
	return P and P.Character
end

local function GetRoot(P)
	local C = GetCharacter(P)
	return C and C:FindFirstChild("HumanoidRootPart")
end

local function GetHumanoid(P)
	local C = GetCharacter(P)
	return C and C:FindFirstChildOfClass("Humanoid")
end

local function IsAlive(P)
	local H = GetHumanoid(P)
	return H and H.Health > 0
end

--==================================================
--// GUI
--==================================================

local GUI = Instance.new("ScreenGui")
GUI.Name = "VendettaHub"
GUI.ResetOnSpawn = false
GUI.IgnoreGuiInset = true
GUI.DisplayOrder = 999999
GUI.Parent = PG

--==================================================
--// PREMIUM INTRO
--==================================================

local Intro = Instance.new("Frame")
Intro.Size = UDim2.fromScale(1,1)
Intro.BackgroundColor3 = Color3.fromRGB(5,5,8)
Intro.BorderSizePixel = 0
Intro.ZIndex = 100
Intro.Parent = GUI

local IntroTitle = Instance.new("TextLabel")
IntroTitle.Size = UDim2.new(1,0,0,70)
IntroTitle.Position = UDim2.new(0,0,.37,0)
IntroTitle.BackgroundTransparency = 1
IntroTitle.Text = "VENDETTA HUB"
IntroTitle.TextColor3 = Color3.fromRGB(235,45,45)
IntroTitle.TextScaled = true
IntroTitle.Font = Enum.Font.GothamBlack
IntroTitle.ZIndex = 101
IntroTitle.Parent = Intro

local IntroDesc = Instance.new("TextLabel")
IntroDesc.Size = UDim2.new(1,0,0,35)
IntroDesc.Position = UDim2.new(0,0,.49,0)
IntroDesc.BackgroundTransparency = 1
IntroDesc.Text = "Made from the best, To be used by the best"
IntroDesc.TextColor3 = Color3.fromRGB(190,190,200)
IntroDesc.TextSize = 15
IntroDesc.Font = Enum.Font.GothamMedium
IntroDesc.ZIndex = 101
IntroDesc.Parent = Intro


task.spawn(function()
	task.wait(2)

	local FadeInfo = TweenInfo.new(1.2,Enum.EasingStyle.Quad,Enum.EasingDirection.Out)

	TS:Create(Intro,FadeInfo,{BackgroundTransparency = 1}):Play()
	TS:Create(IntroTitle,FadeInfo,{TextTransparency = 1}):Play()
	TS:Create(IntroDesc,FadeInfo,{TextTransparency = 1}):Play()

	task.wait(1.3)

	if Intro then
		Intro:Destroy()
	end
end)

--==================================================
--// MAIN
--==================================================

local Main = Instance.new("Frame")
Main.Size = UDim2.fromOffset(500,390)
Main.Position = UDim2.new(.5,-250,.5,-195)
Main.BackgroundColor3 = Color3.fromRGB(9,9,13)
Main.BorderSizePixel = 0
Main.Active = true
Main.Parent = GUI

Instance.new("UICorner",Main).CornerRadius = UDim.new(0,16)

-- Bottom profile display
local Profile = Instance.new("Frame",Main)
Profile.Size = UDim2.fromOffset(150,50)
Profile.Position = UDim2.new(1,-160,1,-58)
Profile.BackgroundTransparency = 1

local Avatar = Instance.new("ImageLabel",Profile)
Avatar.Size = UDim2.fromOffset(42,42)
Avatar.Position = UDim2.fromOffset(0,4)
Avatar.BackgroundColor3 = Color3.fromRGB(20,20,25)
Avatar.BorderSizePixel = 0
pcall(function()
    Avatar.Image = Players:GetUserThumbnailAsync(
        LP.UserId,
        Enum.ThumbnailType.HeadShot,
        Enum.ThumbnailSize.Size100x100
    )
end)
Instance.new("UICorner",Avatar).CornerRadius = UDim.new(1,0)

local ProfileName = Instance.new("TextLabel",Profile)
ProfileName.Size = UDim2.fromOffset(100,42)
ProfileName.Position = UDim2.fromOffset(50,4)
ProfileName.BackgroundTransparency = 1
ProfileName.Text = "@"..LP.Name
ProfileName.TextColor3 = Color3.fromRGB(235,235,240)
ProfileName.Font = Enum.Font.GothamBold
ProfileName.TextSize = 12
ProfileName.TextXAlignment = Enum.TextXAlignment.Left



local MainStroke = Instance.new("UIStroke",Main)
MainStroke.Thickness = 2

local MainGradient = Instance.new("UIGradient",Main)
MainGradient.Color = ColorSequence.new({
	ColorSequenceKeypoint.new(0,Color3.fromRGB(35,8,8)),
	ColorSequenceKeypoint.new(.5,Color3.fromRGB(9,9,13)),
	ColorSequenceKeypoint.new(1,Color3.fromRGB(25,5,5))
})

--==================================================
--// TOP BAR
--==================================================

local Top = Instance.new("Frame",Main)
Top.Size = UDim2.new(1,0,0,62)
Top.BackgroundTransparency = 1
Top.Active = true

local Title = Instance.new("TextLabel",Top)
Title.Size = UDim2.new(1,-120,1,0)
Title.Position = UDim2.fromOffset(18,0)
Title.BackgroundTransparency = 1
Title.Text = "VENDETTA  •  HUB"
Title.TextColor3 = Color3.fromRGB(235,45,45)
Title.TextSize = 20
Title.Font = Enum.Font.GothamBlack
Title.TextXAlignment = Enum.TextXAlignment.Left

local SubTitle = Instance.new("TextLabel",Top)
SubTitle.Size = UDim2.new(1,-140,0,20)
SubTitle.Position = UDim2.fromOffset(19,37)
SubTitle.BackgroundTransparency = 1
SubTitle.Text = "PREMIUM EDITION"
SubTitle.TextColor3 = Color3.fromRGB(130,130,140)
SubTitle.TextSize = 9
SubTitle.Font = Enum.Font.GothamBold
SubTitle.TextXAlignment = Enum.TextXAlignment.Left

local Minimize = Instance.new("TextButton",Top)
Minimize.Size = UDim2.fromOffset(40,40)
Minimize.Position = UDim2.new(1,-50,0,10)
Minimize.Text = "—"
Minimize.TextSize = 20
Minimize.Font = Enum.Font.GothamBold
Minimize.TextColor3 = Color3.fromRGB(235,45,45)
Minimize.BackgroundColor3 = Color3.fromRGB(25,23,18)
Minimize.BorderSizePixel = 0

Instance.new("UICorner",Minimize).CornerRadius = UDim.new(0,10)

--==================================================
--// SIDEBAR
--==================================================

local Side = Instance.new("Frame",Main)
Side.Size = UDim2.fromOffset(122,315)
Side.Position = UDim2.fromOffset(8,68)
Side.BackgroundColor3 = Color3.fromRGB(13,13,18)
Side.BorderSizePixel = 0

Instance.new("UICorner",Side).CornerRadius = UDim.new(0,12)

local SideStroke = Instance.new("UIStroke",Side)
SideStroke.Thickness = 1
SideStroke.Transparency = .5

local Content = Instance.new("Frame",Main)
Content.Size = UDim2.new(1,-140,1,-78)
Content.Position = UDim2.fromOffset(134,68)
Content.BackgroundTransparency = 1

local Pages = {}
local Tabs = {}

local function CreatePage(Name)
	local Page = Instance.new("ScrollingFrame",Content)
	Page.Name = Name
	Page.Size = UDim2.fromScale(1,1)
	Page.BackgroundTransparency = 1
	Page.BorderSizePixel = 0
	Page.ScrollBarThickness = 3
	Page.ScrollBarImageColor3 = Color3.fromRGB(235,45,45)
	Page.CanvasSize = UDim2.new(0,0,0,0)
	Page.AutomaticCanvasSize = Enum.AutomaticSize.Y
	Page.Visible = false

	Pages[Name] = Page
	return Page
end

local function CreateTab(Name,Y,Icon)
	local B = Instance.new("TextButton",Side)
	B.Size = UDim2.new(1,-12,0,32)
	B.Position = UDim2.fromOffset(6,Y)
	B.Text = Icon.."  "..Name
	B.TextSize = 12
	B.Font = Enum.Font.GothamSemibold
	B.TextColor3 = Color3.fromRGB(170,170,180)
	B.BackgroundColor3 = Color3.fromRGB(19,19,25)
	B.BorderSizePixel = 0

	Instance.new("UICorner",B).CornerRadius = UDim.new(0,9)

	Tabs[Name] = B
	return B
end

local Visuals = CreatePage("Visuals")
local Items = CreatePage("Items")
local Combat = CreatePage("Combat")
local Mobility = CreatePage("Mobility")
local FarmPage = CreatePage("Farm")
local Server = CreatePage("Server")
local Information = CreatePage("Information")
local Settings = CreatePage("Settings")
local FindPage = CreatePage("Find")

CreateTab("Visuals",5,"◉")
CreateTab("Items",39,"◆")
CreateTab("Combat",73,"⚔")
CreateTab("Mobility",107,"↗")
CreateTab("Farm",141,"⌁")
CreateTab("Server",175,"◎")
CreateTab("Info",209,"ℹ")
CreateTab("Settings",243,"⚙")
CreateTab("Find",277,"⌖")

local function ShowPage(Name)
	for N,P in pairs(Pages) do
		P.Visible = N == Name
	end

	for N,B in pairs(Tabs) do
		if (N == "Info" and Name == "Information") or N == Name then
			B.BackgroundColor3 = Color3.fromRGB(65,18,18)
			B.TextColor3 = Color3.fromRGB(235,45,45)
		else
			B.BackgroundColor3 = Color3.fromRGB(19,19,25)
			B.TextColor3 = Color3.fromRGB(170,170,180)
		end
	end
end

for Name,PageName in pairs({
	Visuals = "Visuals",
	Items = "Items",
	Combat = "Combat",
	Mobility = "Mobility",
	Farm = "Farm",
	Server = "Server",
	Info = "Information",
	Settings = "Settings",
	Find = "Find"
}) do
	Tabs[Name].MouseButton1Click:Connect(function()
		ShowPage(PageName)
	end)
end

ShowPage("Visuals")

--==================================================
--// UI HELPERS
--==================================================

local function Button(Parent,Text,Y)
	local B = Instance.new("TextButton",Parent)
	B.Size = UDim2.new(1,-12,0,40)
	B.Position = UDim2.fromOffset(6,Y)
	B.Text = Text
	B.TextSize = 13
	B.Font = Enum.Font.GothamSemibold
	B.TextColor3 = Color3.fromRGB(240,240,245)
	B.BackgroundColor3 = Color3.fromRGB(19,20,27)
	B.BorderSizePixel = 0

	Instance.new("UICorner",B).CornerRadius = UDim.new(0,10)

	local Stroke = Instance.new("UIStroke",B)
	Stroke.Thickness = 1
	Stroke.Transparency = .8

	return B
end

local function Label(Parent,Text,Y)
	local L = Instance.new("TextLabel",Parent)
	L.Size = UDim2.new(1,-12,0,30)
	L.Position = UDim2.fromOffset(6,Y)
	L.BackgroundTransparency = 1
	L.Text = Text
	L.TextSize = 12
	L.Font = Enum.Font.GothamMedium
	L.TextColor3 = Color3.fromRGB(145,145,155)
	L.TextXAlignment = Enum.TextXAlignment.Left
	return L
end

--==================================================
--// VISUALS — CONTROLS ONLY
--==================================================

local HealthButton = Button(Visuals,"Health ESP   •   OFF",8)
local NameDistanceButton = Button(Visuals,"Name + Distance ESP   •   OFF",52)
local FluteESPButton = Button(Visuals,"Flute ESP   •   OFF",96)
local EarringsESPButton = Button(Visuals,"Earrings ESP   •   OFF",140)

--==================================================
--// ITEMS — CONTROLS ONLY
--==================================================

local FluteTween = Button(Items,"Tween → Flute",8)
local EarringsTween = Button(Items,"Tween → Earrings",52)
local FlutePrompt = Button(Items,"Instant Flute Interaction",96)
local EarringsPrompt = Button(Items,"Instant Earrings Interaction",140)

--==================================================
--// COMBAT — CONTROLS ONLY
--==================================================

local BlockButton = Button(Combat,"Auto Block   •   OFF",8)
local ArmButton = Button(Combat,"No Arm   •   OFF",52)
local FriendIgnoreButton = Button(Combat,"Friend Ignore   •   OFF",96)
local HitboxButton = Button(Combat,"Hitbox ESP   •   OFF",140)
local BreathingButton = Button(Combat,"Auto Breathing   •   OFF",184)
local KatanaButton = Button(Combat,"Katana Hide   •   OFF",228)

--==================================================
--// MOBILITY — CONTROLS ONLY
--==================================================

local FlyButton = Button(Mobility,"Fly   •   OFF",8)
local NoclipButton = Button(Mobility,"Noclip   •   OFF",52)
local DashButton = Button(Mobility,"Infinite Dash   •   OFF",96)

Label(Mobility,"Flight Speed",100)

local SpeedBox = Instance.new("TextBox",Mobility)
SpeedBox.Size = UDim2.fromOffset(90,38)
SpeedBox.Position = UDim2.fromOffset(6,130)
SpeedBox.Text = "50"
SpeedBox.TextSize = 15
SpeedBox.Font = Enum.Font.GothamBold
SpeedBox.TextColor3 = Color3.new(1,1,1)
SpeedBox.BackgroundColor3 = Color3.fromRGB(19,20,27)
SpeedBox.BorderSizePixel = 0
SpeedBox.ClearTextOnFocus = false

Instance.new("UICorner",SpeedBox).CornerRadius = UDim.new(0,9)

local Minus = Button(Mobility,"−",178)
Minus.Size = UDim2.fromOffset(55,38)

local Plus = Button(Mobility,"+",222)
Plus.Size = UDim2.fromOffset(55,38)

local SpeedDisplay = Label(Mobility,"50",178)
SpeedDisplay.Position = UDim2.fromOffset(72,178)
SpeedDisplay.Size = UDim2.new(1,-75,0,82)
SpeedDisplay.TextSize = 25
SpeedDisplay.TextColor3 = Color3.fromRGB(235,45,45)

--==================================================
--// FARM — CONTROLS ONLY
--==================================================

Label(FarmPage,"Target Player",8)

local TargetBox = Instance.new("TextBox",FarmPage)
TargetBox.Size = UDim2.new(1,-12,0,40)
TargetBox.Position = UDim2.fromOffset(6,38)
TargetBox.PlaceholderText = "Enter username..."
TargetBox.Text = ""
TargetBox.TextSize = 13
TargetBox.Font = Enum.Font.GothamMedium
TargetBox.TextColor3 = Color3.new(1,1,1)
TargetBox.PlaceholderColor3 = Color3.fromRGB(110,110,120)
TargetBox.BackgroundColor3 = Color3.fromRGB(19,20,27)
TargetBox.BorderSizePixel = 0

Instance.new("UICorner",TargetBox).CornerRadius = UDim.new(0,10)

local FarmButton = Button(FarmPage,"Farm   •   OFF",88)

--==================================================

DashButton.MouseButton1Click:Connect(function()
    InfiniteDash = not InfiniteDash
    DashButton.Text = "Infinite Dash   •   "..(InfiniteDash and "ON" or "OFF")
end)

task.spawn(function()
    while GUI.Parent do
        if InfiniteDash then
            pcall(function()
                game:GetService("ReplicatedStorage").DashWithNoDelay:InvokeServer("Dash")
            end)
        end
        task.wait(.2)
    end
end)

--// SERVER — CONTROLS ONLY
--==================================================

local RejoinButton = Button(Server,"Rejoin Current Server",8)
local ServerHopButton = Button(Server,"Server Hop",52)

--==================================================
--// INFORMATION — EXPLANATIONS
--==================================================

Label(Information,"PC CONTROLS",8)

Label(Information,"W / A / S / D     •     Fly movement",38)
Label(Information,"SPACE             •     Fly upward",63)
Label(Information,"LEFT CTRL         •     Fly downward",88)
Label(Information,"RIGHT CTRL        •     Toggle Fly",113)
Label(Information,"+ / -              •     Change Fly Speed",138)

Label(Information,"MOBILE CONTROLS",178)

local MobileInfo = Label(
    Information,
    "Use the ▲ / ▼ buttons while Fly is enabled.",
    208
)
MobileInfo.TextWrapped = true

--==================================================
--// SETTINGS — CONTROLS ONLY
--==================================================

local RGBButton = Button(Settings,"Red Border   •   ON",8)
local HideButton = Button(Settings,"Hide / Minimize",52)

--==================================================
--// FIND — TRAINERS + ICE SHARD
--==================================================

Label(FindPage,"TRAINERS",8)

local TrainerButtons = {}
local trainerY = 42
for Name in pairs(TrainerPoints) do
    TrainerButtons[Name] = Button(FindPage,"Tween → "..Name,trainerY)
    trainerY += 44
end

Label(FindPage,"ACTIVE ICE SHARD",trainerY + 4)
local IceESPButton = Button(FindPage,"Ice Shard ESP   •   OFF",trainerY + 36)
local IceTweenButton = Button(FindPage,"Tween → Active Ice Shard",trainerY + 80)

--==================================================
--// DRAGGING
--==================================================

local function MakeDraggable(Object)

	local Dragging = false
	local Start
	local StartPosition

	Object.InputBegan:Connect(function(Input)
		if Input.UserInputType == Enum.UserInputType.Touch
		or Input.UserInputType == Enum.UserInputType.MouseButton1 then
			Dragging = true
			Start = Input.Position
			StartPosition = Object.Position
		end
	end)

	Object.InputEnded:Connect(function(Input)
		if Input.UserInputType == Enum.UserInputType.Touch
		or Input.UserInputType == Enum.UserInputType.MouseButton1 then
			Dragging = false
		end
	end)

	UIS.InputChanged:Connect(function(Input)
		if not Dragging then return end

		if Input.UserInputType == Enum.UserInputType.Touch
		or Input.UserInputType == Enum.UserInputType.MouseMovement then

			local Delta = Input.Position - Start

			Object.Position = UDim2.new(
				StartPosition.X.Scale,
				StartPosition.X.Offset + Delta.X,
				StartPosition.Y.Scale,
				StartPosition.Y.Offset + Delta.Y
			)
		end
	end)
end

MakeDraggable(Main)

--==================================================
--// MINIMIZED
--==================================================

local Mini = Instance.new("TextButton",GUI)
Mini.Size = UDim2.fromOffset(62,62)
Mini.Position = Main.Position
Mini.Text = "V"
Mini.TextSize = 28
Mini.Font = Enum.Font.GothamBlack
Mini.TextColor3 = Color3.fromRGB(235,45,45)
Mini.BackgroundColor3 = Color3.fromRGB(10,10,14)
Mini.BorderSizePixel = 0
Mini.Visible = false

Instance.new("UICorner",Mini).CornerRadius = UDim.new(1,0)

local MiniBorder = Instance.new("UIStroke",Mini)
MiniBorder.Thickness = 2

MakeDraggable(Mini)

local function MinimizeGUI()
	Mini.Position = Main.Position
	Main.Visible = false
	Mini.Visible = true
end

Minimize.MouseButton1Click:Connect(MinimizeGUI)
HideButton.MouseButton1Click:Connect(MinimizeGUI)

Mini.MouseButton1Click:Connect(function()
	Main.Position = Mini.Position
	Mini.Visible = false
	Main.Visible = true
end)

--==================================================
--// RGB PREMIUM BORDER
--==================================================

RS.RenderStepped:Connect(function()

	if RGB then
		local C = Color3.fromHSV((tick() % 6) / 6,.75,1)
		MainStroke.Color = C
		MiniBorder.Color = C
	else
		MainStroke.Color = Color3.fromRGB(235,45,45)
		MiniBorder.Color = Color3.fromRGB(235,45,45)
	end
end)

RGBButton.MouseButton1Click:Connect(function()
	RGB = not RGB
	RGBButton.Text = "Red Border   •   " .. (RGB and "ON" or "OFF")
end)

--==================================================
--// CHARACTER
--==================================================

local function UpdateCharacter(C)
	Character = C
	HRP = C:WaitForChild("HumanoidRootPart")
	Humanoid = C:WaitForChild("Humanoid")
end

LP.CharacterAdded:Connect(UpdateCharacter)

--==================================================
--// ESP CLEANUP
--==================================================

local function ClearESP(Key)

	if ESPObjects[Key] then
		for _,Object in ipairs(ESPObjects[Key]) do
			if Object and Object.Parent then
				Object:Destroy()
			end
		end
	end

	ESPObjects[Key] = nil
end

--==================================================
--// HEALTH ESP
--==================================================

local function CreateHealthESP(P)

	if P == LP then return end

	local Root = GetRoot(P)
	local Hum = GetHumanoid(P)

	if not Root or not Hum then return end

	local Key = "HEALTH_" .. P.UserId

	ClearESP(Key)

	local Billboard = Instance.new("BillboardGui")
	Billboard.Name = "VendettaHealthESP"
	Billboard.Size = UDim2.fromOffset(190,90)
	Billboard.StudsOffset = Vector3.new(0,3.5,0)
	Billboard.AlwaysOnTop = true
	Billboard.Parent = Root

	local Holder = Instance.new("Frame",Billboard)
	Holder.Size = UDim2.fromScale(1,1)
	Holder.BackgroundTransparency = 1

	local Name = Instance.new("TextLabel",Holder)
	Name.Size = UDim2.new(1,0,0,22)
	Name.BackgroundTransparency = 1
	Name.TextColor3 = Color3.fromRGB(235,45,45)
	Name.TextStrokeTransparency = .25
	Name.Font = Enum.Font.GothamBold
	Name.TextSize = 13

	local HealthText = Instance.new("TextLabel",Holder)
	HealthText.Size = UDim2.new(1,0,0,20)
	HealthText.Position = UDim2.fromOffset(0,21)
	HealthText.BackgroundTransparency = 1
	HealthText.TextColor3 = Color3.new(1,1,1)
	HealthText.TextStrokeTransparency = .25
	HealthText.Font = Enum.Font.GothamSemibold
	HealthText.TextSize = 11

	local BarBG = Instance.new("Frame",Holder)
	BarBG.Size = UDim2.new(.85,0,0,8)
	BarBG.Position = UDim2.new(.075,0,0,45)
	BarBG.BackgroundColor3 = Color3.fromRGB(30,30,35)
	BarBG.BorderSizePixel = 0

	Instance.new("UICorner",BarBG).CornerRadius = UDim.new(1,0)

	local Bar = Instance.new("Frame",BarBG)
	Bar.Size = UDim2.fromScale(1,1)
	Bar.BackgroundColor3 = Color3.fromRGB(235,45,45)
	Bar.BorderSizePixel = 0

	Instance.new("UICorner",Bar).CornerRadius = UDim.new(1,0)

	local Distance = Instance.new("TextLabel",Holder)
	Distance.Size = UDim2.new(1,0,0,20)
	Distance.Position = UDim2.fromOffset(0,55)
	Distance.BackgroundTransparency = 1
	Distance.TextColor3 = Color3.fromRGB(175,175,185)
	Distance.TextStrokeTransparency = .3
	Distance.Font = Enum.Font.GothamMedium
	Distance.TextSize = 10

	ESPObjects[Key] = {Billboard}

	task.spawn(function()

		while Billboard.Parent and HealthESP do

			local MyRoot = HRP
			local TheirRoot = GetRoot(P)
			local TheirHum = GetHumanoid(P)

			if not MyRoot or not TheirRoot or not TheirHum then break end

			local Max = math.max(TheirHum.MaxHealth,1)
			local Current = math.clamp(TheirHum.Health,0,Max)
			local Percent = Current / Max

			Name.Text = P.Name
			HealthText.Text = math.floor(Current) .. " / " .. math.floor(Max)
			Bar.Size = UDim2.new(Percent,0,1,0)

			Distance.Text =
				math.floor((MyRoot.Position - TheirRoot.Position).Magnitude)
				.. " studs"

			task.wait(.12)
		end
	end)
end

--==================================================
--// NAME + DISTANCE ESP
--==================================================

local function CreateNameDistanceESP(P)

	if P == LP then return end

	local Root = GetRoot(P)
	if not Root then return end

	local Key = "NAME_" .. P.UserId

	ClearESP(Key)

	local Billboard = Instance.new("BillboardGui")
	Billboard.Name = "VendettaNameESP"
	Billboard.Size = UDim2.fromOffset(180,50)
	Billboard.StudsOffset = Vector3.new(0,3,0)
	Billboard.AlwaysOnTop = true
	Billboard.Parent = Root

	local Text = Instance.new("TextLabel",Billboard)
	Text.Size = UDim2.fromScale(1,1)
	Text.BackgroundTransparency = 1
	Text.TextColor3 = Color3.fromRGB(235,45,45)
	Text.TextStrokeTransparency = .2
	Text.Font = Enum.Font.GothamBold
	Text.TextSize = 14

	ESPObjects[Key] = {Billboard}

	task.spawn(function()

		while Billboard.Parent and NameDistanceESP do

			local MyRoot = HRP
			local TheirRoot = GetRoot(P)

			if not MyRoot or not TheirRoot then break end

			local Distance =
				math.floor((MyRoot.Position - TheirRoot.Position).Magnitude)

			Text.Text = P.Name .. "  •  " .. Distance .. " studs"

			task.wait(.15)
		end
	end)
end

local function RefreshPlayers()

	for _,P in ipairs(Players:GetPlayers()) do

		if P ~= LP then

			if HealthESP then
				CreateHealthESP(P)
			end

			if NameDistanceESP then
				CreateNameDistanceESP(P)
			end
		end
	end
end

HealthButton.MouseButton1Click:Connect(function()

	HealthESP = not HealthESP

	HealthButton.Text =
		"Health ESP   •   " .. (HealthESP and "ON" or "OFF")

	if HealthESP then
		RefreshPlayers()
	else
		for _,P in ipairs(Players:GetPlayers()) do
			ClearESP("HEALTH_"..P.UserId)
		end
	end
end)

NameDistanceButton.MouseButton1Click:Connect(function()

	NameDistanceESP = not NameDistanceESP

	NameDistanceButton.Text =
		"Name + Distance ESP   •   "
		.. (NameDistanceESP and "ON" or "OFF")

	if NameDistanceESP then
		RefreshPlayers()
	else
		for _,P in ipairs(Players:GetPlayers()) do
			ClearESP("NAME_"..P.UserId)
		end
	end
end)

--==================================================
--// FIND FLUTE
--==================================================

local function FindFlute()

	for _,Object in ipairs(workspace:GetDescendants()) do

		if Object.Name == "chatnpcs" then

			local Flute = Object:FindFirstChild("Flute",true)

			if Flute then

				if Flute:IsA("BasePart") then
					return Flute
				end

				local Part =
					Flute:FindFirstChildWhichIsA("BasePart",true)

				if Part then
					return Part
				end
			end
		end
	end

	return nil
end

--==================================================
--// FIND EARRINGS
--==================================================

local function FindEarrings()

	for _,Barrel in ipairs(workspace:GetDescendants()) do

		if Barrel.Name == "Barrel" then

			local Part =
				Barrel:FindFirstChild("Part",true)

			if Part and Part:IsA("BasePart") then
				return Part
			end
		end
	end

	return nil
end

--==================================================
--// OBJECT ESP
--==================================================

local function CreateObjectESP(Part,TextFunction,Key)

	if not Part then return end

	ClearESP(Key)

	local Billboard = Instance.new("BillboardGui")
	Billboard.Name = "VendettaESP"
	Billboard.Size = UDim2.fromOffset(190,45)
	Billboard.StudsOffset = Vector3.new(0,3,0)
	Billboard.AlwaysOnTop = true
	Billboard.Parent = Part

	local Text = Instance.new("TextLabel",Billboard)
	Text.Size = UDim2.fromScale(1,1)
	Text.BackgroundTransparency = 1
	Text.Font = Enum.Font.GothamBold
	Text.TextSize = 14
	Text.TextColor3 = Color3.fromRGB(235,45,45)
	Text.TextStrokeTransparency = .2

	ESPObjects[Key] = {Billboard}

	task.spawn(function()
		while Billboard.Parent do
			Text.Text = TextFunction()
			task.wait(.15)
		end
	end)
end

FluteESPButton.MouseButton1Click:Connect(function()

	FluteESP = not FluteESP

	FluteESPButton.Text =
		"Flute ESP   •   " .. (FluteESP and "ON" or "OFF")

	if FluteESP then

		local Part = FindFlute()

		if Part then
			CreateObjectESP(
				Part,
				function()
					return "♫  FLUTE"
				end,
				"FLUTE"
			)
		end

	else
		ClearESP("FLUTE")
	end
end)

EarringsESPButton.MouseButton1Click:Connect(function()

	EarringsESP = not EarringsESP

	EarringsESPButton.Text =
		"Earrings ESP   •   " .. (EarringsESP and "ON" or "OFF")

	if EarringsESP then

		local Part = FindEarrings()

		if Part then
			CreateObjectESP(
				Part,
				function()
					return "◈  EARRINGS"
				end,
				"EARRINGS"
			)
		end

	else
		ClearESP("EARRINGS")
	end
end)

--==================================================
--// TRAINER TWEEN + ICE SHARD
--==================================================

local function TweenToPosition(Position)
    if not HRP then return end

    if TrainerTween then
        TrainerTween:Cancel()
        TrainerTween = nil
    end

    local Distance = (Position - HRP.Position).Magnitude
    local Duration = Distance <= 500 and 3 or 6

    local Goal = CFrame.new(Position + Vector3.new(0,3,0))
    TrainerTween = TS:Create(
        HRP,
        TweenInfo.new(Duration,Enum.EasingStyle.Quad,Enum.EasingDirection.InOut),
        {CFrame = Goal}
    )
    TrainerTween:Play()
    TrainerTween.Completed:Once(function()
        TrainerTween = nil
    end)
end

for Name,ButtonObject in pairs(TrainerButtons) do
    ButtonObject.MouseButton1Click:Connect(function()
        TweenToPosition(TrainerPoints[Name])
    end)
end

local function GetIceShard()
    return workspace:FindFirstChild("ActiveIceShard")
end

local function GetIceShardPart(Shard)
    if not Shard then return nil end
    if Shard:IsA("BasePart") then return Shard end
    if Shard:IsA("Model") and Shard.PrimaryPart then return Shard.PrimaryPart end

    local Named = Shard:FindFirstChild("IceShard",true)
    if Named and Named:IsA("BasePart") then return Named end

    local Ray = Shard:FindFirstChild("IceShardRay",true)
    if Ray and Ray:IsA("BasePart") then return Ray end

    local Largest,Volume = nil,0
    for _,Object in ipairs(Shard:GetDescendants()) do
        if Object:IsA("BasePart") then
            local S = Object.Size
            local V = S.X*S.Y*S.Z
            if V > Volume then
                Volume = V
                Largest = Object
            end
        end
    end
    return Largest
end

local function UpdateIceShardESP()
    local Shard = GetIceShard()
    if not Shard then return end

    local H = Shard:FindFirstChild("VendettaIceShardESP")
    if not H then
        H = Instance.new("Highlight")
        H.Name = "VendettaIceShardESP"
        H.Adornee = Shard
        H.FillColor = Color3.fromRGB(210,25,25)
        H.OutlineColor = Color3.fromRGB(255,90,90)
        H.FillTransparency = 0.25
        H.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        H.Parent = Shard
    end
    H.Enabled = IceShardESP
end

IceESPButton.MouseButton1Click:Connect(function()
    IceShardESP = not IceShardESP
    IceESPButton.Text = "Ice Shard ESP   •   "..(IceShardESP and "ON" or "OFF")
    UpdateIceShardESP()
end)

IceTweenButton.MouseButton1Click:Connect(function()
    local Shard = GetIceShard()
    local Part = GetIceShardPart(Shard)
    if Part then
        local Distance = (Part.Position - HRP.Position).Magnitude
        local Duration = Distance <= 500 and 3 or 6

        if IceShardTween then
            IceShardTween:Cancel()
        end

        IceShardTween = TS:Create(
            HRP,
            TweenInfo.new(Duration,Enum.EasingStyle.Quad,Enum.EasingDirection.InOut),
            {CFrame = Part.CFrame * CFrame.new(0,3,0)}
        )
        IceTweenButton.Text = "Tweening • "..Duration.."s"
        IceShardTween:Play()
        IceShardTween.Completed:Once(function()
            IceShardTween = nil
            if IceTweenButton.Parent then
                IceTweenButton.Text = "Tween → Active Ice Shard"
            end
        end)
    else
        IceTweenButton.Text = "Active Ice Shard Not Found"
        task.delay(1.2,function()
            if IceTweenButton.Parent then
                IceTweenButton.Text = "Tween → Active Ice Shard"
            end
        end)
    end
end)

workspace.ChildAdded:Connect(function(Object)
    if Object.Name == "ActiveIceShard" then
        task.wait()
        UpdateIceShardESP()
    end
end)

workspace.ChildRemoved:Connect(function(Object)
    if Object.Name == "ActiveIceShard" then
        task.wait()
        UpdateIceShardESP()
    end
end)

task.spawn(function()
    while GUI.Parent do
        if IceShardESP then UpdateIceShardESP() end
        task.wait(0.25)
    end
end)

--==================================================
--// ITEM TWEEN
--==================================================

local function TweenTo(Part)

	if not Part or not HRP then return end

	local Goal = Part.CFrame * CFrame.new(0,3,0)

	local Tween =
		TS:Create(
			HRP,
			TweenInfo.new(3,Enum.EasingStyle.Quad,Enum.EasingDirection.InOut),
			{CFrame = Goal}
		)

	Tween:Play()
	return Tween
end

FluteTween.MouseButton1Click:Connect(function()
	local Part = FindFlute()
	if Part then TweenTo(Part) end
end)

EarringsTween.MouseButton1Click:Connect(function()
	local Part = FindEarrings()
	if Part then TweenTo(Part) end
end)

--==================================================
--// PROMPT INTERACTION
--==================================================

local function FirePromptsUnder(Object)

	if not Object then return false end

	local Found = false

	for _,Prompt in ipairs(Object:GetDescendants()) do

		if Prompt:IsA("ProximityPrompt") then

			Found = true

			pcall(function()
				fireproximityprompt(Prompt)
			end)
		end
	end

	if Object:IsA("ProximityPrompt") then

		Found = true

		pcall(function()
			fireproximityprompt(Object)
		end)
	end

	return Found
end

FlutePrompt.MouseButton1Click:Connect(function()
	local Flute = FindFlute()
	if Flute then
		FirePromptsUnder(Flute)
	end
end)

EarringsPrompt.MouseButton1Click:Connect(function()
	local Earrings = FindEarrings()
	if Earrings then
		FirePromptsUnder(Earrings)
	end
end)

--==================================================
--// NO ARM
--==================================================

local function SetArms(Hidden)

	if not Character then return end

	for _,Object in ipairs(Character:GetDescendants()) do

		if Object:IsA("BasePart") then

			local Name = Object.Name:lower()

			if Name:find("arm") or Name:find("hand") then
				Object.LocalTransparencyModifier = Hidden and 1 or 0
			end
		end
	end
end

ArmButton.MouseButton1Click:Connect(function()

	NoArm = not NoArm

	ArmButton.Text =
		"No Arm   •   " .. (NoArm and "ON" or "OFF")

	SetArms(NoArm)
end)

--==================================================
--// VENDETTA AUTO BLOCK
--// Blocks detected moves + StrongAttackWindup.
--==================================================

local BlockRemote = game:GetService("ReplicatedStorage"):WaitForChild("events"):WaitForChild("remote")
local BlockingNow = false

local MoveNames = {
    ["Void Style"] = true, ["Moon Dragon Ringtail"] = true,
    ["Rapid Conquest"] = true, ["Raging Sun"] = true,
    ["Destruction Style"] = true, ["Rampant Arc Rampage"] = true,
    ["Sonic Scream"] = true, ["Crazed Cry of Thunder"] = true,
    ["Body Spike"] = true, ["Ground Spike"] = true,
    ["Rat's ClawOG"] = true, ["Bite And InfectOG"] = true,
    ["Distant Mist"] = true, ["Compound Eye Hexagon"] = true,
    ["Roar"] = true, ["Explosive Slash"] = true,
    ["Unknowing Fire"] = true, ["Clean Storm Wind Tree"] = true,
    ["Dust Whirlwind Cutter"] = true, ["Peonies of Futility"] = true,
    ["Whirling Peach"] = true, ["Water Surface Slash"] = true,
    ["Demon Blade"] = true, ["Leap Kick"] = true,
    ["Brutal Cleave"] = true, ["Thunderclap Flash"] = true,
    ["Flaming Thunder God"] = true, ["Waltz"] = true,
    ["Freezing Clouds"] = true, ["Coil Choke"] = true,
    ["Winding Serpent Slash"] = true, ["Pierce and Extract"] = true,
    ["Rip and Devour"] = true, ["Flesh Seeds"] = true, ["Obi Slash"] = true,
}

local function ShouldIgnore(P)
    if not FriendIgnore or P == LP then
        return false
    end
    local ok, result = pcall(function()
        return LP:IsFriendsWith(P.UserId)
    end)
    return ok and result == true
end

local function TryAutoBlock()
    if BlockingNow or not AutoBlock then return end
    BlockingNow = true

    pcall(function()
        BlockRemote:FireServer("blockstart")
    end)

    task.delay(0.55,function()
        pcall(function()
            BlockRemote:FireServer("blockend")
        end)
        BlockingNow = false
    end)
end

task.spawn(function()
    local lastMoveState = {}

    while GUI.Parent do
        if AutoBlock and HRP then
            for _,Enemy in ipairs(Players:GetPlayers()) do
                if Enemy ~= LP and not ShouldIgnore(Enemy) and Enemy.Character then
                    local Root = GetRoot(Enemy)
                    local Windup = Enemy.Character:FindFirstChild("StrongAttackWindup", true)
                    local CDS = Enemy:FindFirstChild("cds") or Enemy.Character:FindFirstChild("cds")

                    if Root and (Root.Position - HRP.Position).Magnitude <= 50 then
                        if Windup then
                            TryAutoBlock()
                        end

                        if CDS then
                            lastMoveState[Enemy] = lastMoveState[Enemy] or {}
                            for MoveName in pairs(MoveNames) do
                                local Active = CDS:FindFirstChild(MoveName) ~= nil
                                local Previous = lastMoveState[Enemy][MoveName]

                                if Active and not Previous then
                                    TryAutoBlock()
                                end

                                lastMoveState[Enemy][MoveName] = Active
                            end
                        end
                    end
                end
            end
        end
        task.wait(0.05)
    end
end)

BlockButton.MouseButton1Click:Connect(function()
    AutoBlock = not AutoBlock
    BlockButton.Text = "Auto Block   •   " .. (AutoBlock and "ON" or "OFF")
    BlockButton.BackgroundColor3 = AutoBlock and Color3.fromRGB(70,22,22) or Color3.fromRGB(19,20,27)
end)

--==================================================
--// FRIEND IGNORE + HITBOX ESP
--==================================================

local HitboxObjects = {}

local function RemoveHitbox(P)
    local H = HitboxObjects[P]
    if H then H:Destroy() end
    HitboxObjects[P] = nil
end

local function UpdateHitbox(P)
    if P == LP or ShouldIgnore(P) or not HitboxESP then
        RemoveHitbox(P)
        return
    end

    local C = P.Character
    if not C then
        RemoveHitbox(P)
        return
    end

    local H = HitboxObjects[P]
    if not H or H.Parent ~= C then
        RemoveHitbox(P)
        H = Instance.new("Highlight")
        H.Name = "VendettaHitboxESP"
        H.Adornee = C
        H.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        H.FillTransparency = 0.75
        H.OutlineTransparency = 0
        H.FillColor = Color3.fromRGB(210,25,25)
        H.OutlineColor = Color3.fromRGB(255,80,80)
        H.Parent = C
        HitboxObjects[P] = H
    end
end

local function UpdateAllHitbox()
    for _,P in ipairs(Players:GetPlayers()) do
        if P ~= LP then UpdateHitbox(P) end
    end
end

FriendIgnoreButton.MouseButton1Click:Connect(function()
    FriendIgnore = not FriendIgnore
    FriendIgnoreButton.Text = "Friend Ignore   •   "..(FriendIgnore and "ON" or "OFF")
    UpdateAllHitbox()
end)

HitboxButton.MouseButton1Click:Connect(function()
    HitboxESP = not HitboxESP
    HitboxButton.Text = "Hitbox ESP   •   "..(HitboxESP and "ON" or "OFF")
    UpdateAllHitbox()
end)

Players.PlayerRemoving:Connect(function(P)
    RemoveHitbox(P)
end)

task.spawn(function()
    while GUI.Parent do
        if HitboxESP then UpdateAllHitbox() end
        task.wait(0.25)
    end
end)

--==================================================
--// AUTO BREATHING / KATANA HIDE
--==================================================

local FeatureRemote = game:GetService("ReplicatedStorage"):WaitForChild("events"):WaitForChild("remote")

BreathingButton.MouseButton1Click:Connect(function()
    AutoBreathing = not AutoBreathing
    BreathingButton.Text = "Auto Breathing   •   "..(AutoBreathing and "ON" or "OFF")
end)

task.spawn(function()
    while GUI.Parent do
        if AutoBreathing then
            pcall(function()
                FeatureRemote:FireServer("manacharges")
            end)
        end
        task.wait(0.15)
    end
end)

KatanaButton.MouseButton1Click:Connect(function()
    KatanaHidden = not KatanaHidden
    KatanaButton.Text = "Katana Hide   •   "..(KatanaHidden and "ON" or "OFF")
    if KatanaHidden then
        pcall(function()
            FeatureRemote:FireServer("KatanaUneq")
        end)
    end
end)

--==================================================
--// MOBILE FLY BUTTONS
--==================================================

local Up = Instance.new("TextButton",GUI)

Up.Size = UDim2.fromOffset(60,48)
Up.Position = UDim2.new(1,-135,1,-150)
Up.Text = "▲"
Up.TextSize = 23
Up.Font = Enum.Font.GothamBold
Up.TextColor3 = Color3.fromRGB(235,45,45)
Up.BackgroundColor3 = Color3.fromRGB(18,18,24)
Up.BorderSizePixel = 0
Up.Visible = false

Instance.new("UICorner",Up).CornerRadius = UDim.new(0,10)

local Down = Up:Clone()
Down.Parent = GUI
Down.Position = UDim2.new(1,-135,1,-95)
Down.Text = "▼"

local function HoldButton(ButtonObject,Direction)

	ButtonObject.InputBegan:Connect(function(Input)

		if Input.UserInputType == Enum.UserInputType.Touch
		or Input.UserInputType == Enum.UserInputType.MouseButton1 then
			Vertical = Direction
		end
	end)

	ButtonObject.InputEnded:Connect(function(Input)

		if Input.UserInputType == Enum.UserInputType.Touch
		or Input.UserInputType == Enum.UserInputType.MouseButton1 then

			if Vertical == Direction then
				Vertical = 0
			end
		end
	end)
end

HoldButton(Up,1)
HoldButton(Down,-1)

--==================================================
--// FLY SPEED
--==================================================

local function SetSpeed(Value)

	FlySpeed = math.clamp(
		tonumber(Value) or FlySpeed,
		1,
		500
	)

	SpeedBox.Text = tostring(math.floor(FlySpeed))
	SpeedDisplay.Text = tostring(math.floor(FlySpeed))
end

SpeedBox.FocusLost:Connect(function()
	SetSpeed(SpeedBox.Text)
end)

Plus.MouseButton1Click:Connect(function()
	SetSpeed(FlySpeed + 10)
end)

Minus.MouseButton1Click:Connect(function()
	SetSpeed(FlySpeed - 10)
end)

--==================================================
--// FLY
--==================================================

local function SetFlying(State)

	Flying = State

	FlyButton.Text =
		"Fly   •   " .. (Flying and "ON" or "OFF")

	if not Flying and HRP then

		HRP.Anchored = false
		HRP.AssemblyLinearVelocity = Vector3.zero
		HRP.AssemblyAngularVelocity = Vector3.zero
	end
end

FlyButton.MouseButton1Click:Connect(function()
	SetFlying(not Flying)
end)

--==================================================
--// NOCLIP
--==================================================

local function SetNoclip(State)

	Noclip = State

	NoclipButton.Text =
		"Noclip   •   " .. (Noclip and "ON" or "OFF")

	if Noclip then

		if NoclipConnection then
			NoclipConnection:Disconnect()
		end

		NoclipConnection =
			RS.Stepped:Connect(function()

				if not Noclip or not Character then return end

				for _,Object in ipairs(Character:GetDescendants()) do

					if Object:IsA("BasePart") then
						Object.CanCollide = false
					end
				end
			end)

	else

		if NoclipConnection then
			NoclipConnection:Disconnect()
			NoclipConnection = nil
		end
	end
end

NoclipButton.MouseButton1Click:Connect(function()
	SetNoclip(not Noclip)
end)

--==================================================
--// KEYBOARD
--==================================================

UIS.InputBegan:Connect(function(Input,Processed)

	if Processed then return end

	if Input.KeyCode == Enum.KeyCode.Space then
		UpHeld = true
	end

	if Input.KeyCode == Enum.KeyCode.LeftControl then
		DownHeld = true
	end

	if Input.KeyCode == Enum.KeyCode.RightControl then
		SetFlying(not Flying)
	end

	if Input.KeyCode == Enum.KeyCode.Equals then
		SetSpeed(FlySpeed + 5)
	elseif Input.KeyCode == Enum.KeyCode.Minus then
		SetSpeed(FlySpeed - 5)
	end
end)

UIS.InputEnded:Connect(function(Input)

	if Input.KeyCode == Enum.KeyCode.Space then
		UpHeld = false
	end

	if Input.KeyCode == Enum.KeyCode.LeftControl then
		DownHeld = false
	end
end)

--==================================================
--// FLY MOVEMENT
--==================================================

RS.RenderStepped:Connect(function()

	if not Flying or not HRP or not Humanoid then

		Up.Visible = false
		Down.Visible = false

		return
	end

	Up.Visible = true
	Down.Visible = true

	local Camera = workspace.CurrentCamera
	if not Camera then return end

	local Move = Humanoid.MoveDirection

	local Forward = Vector3.new(
		Camera.CFrame.LookVector.X,
		0,
		Camera.CFrame.LookVector.Z
	)

	local Right = Vector3.new(
		Camera.CFrame.RightVector.X,
		0,
		Camera.CFrame.RightVector.Z
	)

	if Forward.Magnitude > 0 then
		Forward = Forward.Unit
	end

	if Right.Magnitude > 0 then
		Right = Right.Unit
	end

	local Horizontal = Vector3.zero

	if Move.Magnitude > 0 then

		Horizontal =
			Forward * Move:Dot(Forward)
			+
			Right * Move:Dot(Right)

		if Horizontal.Magnitude > 1 then
			Horizontal = Horizontal.Unit
		end
	end

	local Y = Vertical

	if UpHeld then
		Y = 1
	elseif DownHeld then
		Y = -1
	end

	HRP.Anchored = false

	HRP.AssemblyLinearVelocity =
		Horizontal * FlySpeed
		+
		Vector3.new(0,Y * FlySpeed,0)

	HRP.AssemblyAngularVelocity = Vector3.zero
end)

--==================================================
--// FARM
--==================================================

local function FindPlayer(Text)

	if not Text or Text == "" then return nil end

	Text = Text:lower()

	for _,P in ipairs(Players:GetPlayers()) do
		if P.Name:lower() == Text then
			return P
		end
	end

	for _,P in ipairs(Players:GetPlayers()) do

		if P.Name:lower():sub(1,#Text) == Text
		or P.DisplayName:lower():sub(1,#Text) == Text then
			return P
		end
	end

	return nil
end

local function StopFarm()

	Farm = false

	FarmButton.Text = "Farm   •   OFF"

	if FarmTween then

		pcall(function()
			FarmTween:Cancel()
		end)

		FarmTween = nil
	end

	if FarmConnection then
		FarmConnection:Disconnect()
		FarmConnection = nil
	end
end

local function StartFarm(Target)

    if not Target or Target == LP then return end

    Farm = true
    FarmTarget = Target
    FarmButton.Text = "Farm   •   ON"

    if FarmConnection then
        FarmConnection:Disconnect()
    end

    FarmConnection = RS.RenderStepped:Connect(function()
        if not Farm then return end

        local Root = GetRoot(FarmTarget)

        if not Root or not IsAlive(FarmTarget) or not HRP then
            StopFarm()
            return
        end

        -- Always update target position and stay behind player
        local Goal = Root.CFrame * CFrame.new(0,0,4)

        FarmTween = TS:Create(
            HRP,
            TweenInfo.new(0.15, Enum.EasingStyle.Linear),
            {CFrame = Goal}
        )

        FarmTween:Play()
    end)
end

FarmButton.MouseButton1Click:Connect(function()

	if Farm then
		StopFarm()
		return
	end

	StartFarm(FindPlayer(TargetBox.Text))
end)

--==================================================
--// SERVER
--==================================================

RejoinButton.MouseButton1Click:Connect(function()

	TeleportService:Teleport(
		game.PlaceId,
		LP
	)
end)

ServerHopButton.MouseButton1Click:Connect(function()

	local Request =
		request
		or http_request
		or (syn and syn.request)

	if not Request then return end

	local Success,Result =
		pcall(function()

			return Request({
				Url =
					"https://games.roblox.com/v1/games/"
					.. game.PlaceId
					.. "/servers/Public?sortOrder=Asc&limit=100",

				Method = "GET"
			})
		end)

	if not Success or not Result then return end

	local Data

	local DecodeSuccess =
		pcall(function()
			Data = HttpService:JSONDecode(Result.Body)
		end)

	if not DecodeSuccess or not Data then return end

	local Servers = {}

	for _,ServerData in ipairs(Data.data or {}) do

		if ServerData.id ~= game.JobId
		and ServerData.playing < ServerData.maxPlayers then

			table.insert(Servers,ServerData.id)
		end
	end

	if #Servers == 0 then return end

	TeleportService:TeleportToPlaceInstance(
		game.PlaceId,
		Servers[math.random(1,#Servers)],
		LP
	)
end)

--==================================================
--// PLAYER EVENTS
--==================================================

Players.PlayerAdded:Connect(function(P)

	P.CharacterAdded:Connect(function()

		task.wait(1)

		if HealthESP then
			CreateHealthESP(P)
		end

		if NameDistanceESP then
			CreateNameDistanceESP(P)
		end
	end)
end)

Players.PlayerRemoving:Connect(function(P)

	ClearESP("HEALTH_" .. P.UserId)
	ClearESP("NAME_" .. P.UserId)

	if FarmTarget == P then
		StopFarm()
	end
end)

--==================================================
--// CHARACTER RESET
--==================================================

LP.CharacterAdded:Connect(function(C)

	Character = C

	HRP = C:WaitForChild("HumanoidRootPart")
	Humanoid = C:WaitForChild("Humanoid")

	task.wait(.5)

	if NoArm then
		SetArms(true)
	end

	if Flying then
		HRP.Anchored = false
	end
end)

--==================================================
--// LATE LOADING ESP
--==================================================

task.spawn(function()

	while GUI.Parent do

		if FluteESP and not ESPObjects.FLUTE then

			local Part = FindFlute()

			if Part then

				CreateObjectESP(
					Part,
					function()
						return "♫  FLUTE"
					end,
					"FLUTE"
				)
			end
		end

		if EarringsESP and not ESPObjects.EARRINGS then

			local Part = FindEarrings()

			if Part then

				CreateObjectESP(
					Part,
					function()
						return "◈  EARRINGS"
					end,
					"EARRINGS"
				)
			end
		end

		if HealthESP or NameDistanceESP then
			RefreshPlayers()
		end

		task.wait(1)
	end
end)

--==================================================
--// LOADED TERRAIN CHECK
--==================================================

task.spawn(function()

	task.wait(2)

	local FluteFound = FindFlute()
	local EarringsFound = FindEarrings()

	if FluteFound and EarringsFound then

		warn("[VENDETTA] Flute and Earrings detected in loaded terrain.")

	elseif FluteFound then

		warn("[VENDETTA] Flute detected in loaded terrain.")
		warn("[VENDETTA] Wander around the map a bit for Earrings ESP/Tween to work.")

	elseif EarringsFound then

		warn("[VENDETTA] Earrings detected in loaded terrain.")
		warn("[VENDETTA] Wander around the map a bit for Flute ESP/Tween to work.")

	else

		warn("[VENDETTA] No Flute or Earrings found in loaded terrain.")
		warn("[VENDETTA] Wander around the map a bit for ESP & Tween to work.")
	end
end)

print("[VENDETTA] Premium Hub loaded.")
