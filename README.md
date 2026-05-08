# imgui-gdi
Dear ImGui with Windows GDI Renderer

## bug fix

```
{1442} normal block at 0x00000220B99420F0, 16 bytes long.
 Data: <            @   > A0 86 95 B9 20 02 00 00 00 02 00 00 40 00 00 00 
{1441} normal block at 0x00000220B99420A0, 16 bytes long.
 Data: <            @   > A0 86 95 B9 20 02 00 00 00 02 00 00 40 00 00 00 
{1440} normal block at 0x00000220B9942050, 16 bytes long.
 Data: <            @   > A0 86 95 B9 20 02 00 00 00 02 00 00 40 00 00 00 
{1439} normal block at 0x00000220B9942000, 16 bytes long.
 Data: <            @   > A0 86 95 B9 20 02 00 00 00 02 00 00 40 00 00 00 
{1438} normal block at 0x00000220B9941FB0, 16 bytes long.
 Data: <            @   > A0 86 95 B9 20 02 00 00 00 02 00 00 40 00 00 00 
{1437} normal block at 0x00000220B9941F60, 16 bytes long.
 Data: <            @   > A0 86 95 B9 20 02 00 00 00 02 00 00 40 00 00 00 
{1436} normal block at 0x00000220B9942870, 16 bytes long.
 Data: <            @   > A0 86 95 B9 20 02 00 00 00 02 00 00 40 00 00 00 
{1435} normal block at 0x00000220B9942820, 16 bytes long.
 Data: <            @   > A0 86 95 B9 20 02 00 00 00 02 00 00 40 00 00 00 
{1434} normal block at 0x00000220B9941D30, 16 bytes long.
 Data: <            @   > A0 86 95 B9 20 02 00 00 00 02 00 00 40 00 00 00 
```



```cpp
//imgui_sw.cpp
void unbind_imgui_painting()
{
	ImGuiIO& io = ImGui::GetIO();
	delete reinterpret_cast<Texture*>(io.Fonts->TexID);
	
	// Here. Need To Use IM_DELETE
	IM_DELETE(io.Fonts);
	
	//io.Fonts,
	io.Fonts = nullptr;
}



void bind_imgui_painting()
{
	ImGuiIO& io = ImGui::GetIO();
	uint8_t* tex_data;
	int font_width, font_height;
	io.Fonts->GetTexDataAsAlpha8(&tex_data, &font_width, &font_height);
	// const auto texture = new Texture{tex_data, font_width, font_height};
	// io.Fonts->TexID = texture;
	
	//If TexID is not nullptr, then set io.Fonts->TexID
	if (io.Fonts->TexID != nullptr) {
		auto texture = ((Texture*)(io.Fonts->TexID));
		texture->pixels = tex_data;
		texture->width = font_width;
		texture->height = font_height;
	}
	//Or New Texture
	else{
		const auto texture = new Texture{ tex_data, font_width, font_height };
		io.Fonts->TexID = texture;
	}
}
```

## Just for me
```cpp
ImGuiIO* ImGui::GetIOPtr() {
    if (GImGui == NULL)return nullptr;
    return &GImGui->IO;
}
```

I need to use it, because I want to use raw pointer.
