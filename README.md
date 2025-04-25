import { useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";

export default function NewIdeaMVP() {
  const [ideas, setIdeas] = useState([]);
  const [seeds, setSeeds] = useState([]);
  const [newSeed, setNewSeed] = useState("");
  const [newIdea, setNewIdea] = useState("");
  const [seedForIdea, setSeedForIdea] = useState("");
  const [price, setPrice] = useState("");
  const [image, setImage] = useState(null);
  const [likedIdeas, setLikedIdeas] = useState({});
  const [searchQuery, setSearchQuery] = useState("");

  const submitSeed = () => {
    if (newSeed.trim()) {
      setSeeds([...seeds, newSeed]);
      setNewSeed("");
    }
  };

  const deleteSeed = (index) => {
    const updatedSeeds = [...seeds];
    updatedSeeds.splice(index, 1);
    setSeeds(updatedSeeds);
    if (seedForIdea === seeds[index]) {
      setSeedForIdea("");
    }
  };

  const submitIdea = () => {
    if (newIdea.trim()) {
      setIdeas([
        ...ideas,
        {
          seed: seedForIdea,
          text: newIdea,
          price,
          image,
          likes: 0,
          comments: [],
          isFavorited: false,
        },
      ]);
      setSeedForIdea("");
      setNewIdea("");
      setPrice("");
      setImage(null);
    }
  };

  const toggleLikeIdea = (index) => {
    const updatedIdeas = [...ideas];
    if (likedIdeas[index]) {
      updatedIdeas[index].likes -= 1;
      const newLikedIdeas = { ...likedIdeas };
      delete newLikedIdeas[index];
      setLikedIdeas(newLikedIdeas);
    } else {
      updatedIdeas[index].likes += 1;
      setLikedIdeas({ ...likedIdeas, [index]: true });
    }
    setIdeas(updatedIdeas);
  };

  const addComment = (index, comment) => {
    const updatedIdeas = [...ideas];
    updatedIdeas[index].comments.push(comment);
    setIdeas(updatedIdeas);
  };

  const toggleFavorite = (index) => {
    const updatedIdeas = [...ideas];
    updatedIdeas[index].isFavorited = !updatedIdeas[index].isFavorited;
    setIdeas(updatedIdeas);
  };

  const filteredIdeas = ideas.filter((idea) =>
    idea.text.toLowerCase().includes(searchQuery.toLowerCase())
  );

  return (
    <div className="p-4 max-w-2xl mx-auto">
      <h1 className="text-xl font-bold mb-4">新想法論壇</h1>
      <Input
        value={searchQuery}
        onChange={(e) => setSearchQuery(e.target.value)}
        placeholder="搜尋創想..."
        className="mb-4"
      />

      {/* 種子構想發表區 */}
      <Card className="p-4 mb-4">
        <h2 className="font-semibold mb-2">發表第0階段：種子構想</h2>
        <Input
          value={newSeed}
          onChange={(e) => setNewSeed(e.target.value)}
          placeholder="輸入種子構想 (靈感、問題...)"
        />
        <Button className="mt-2 mr-2" onClick={submitSeed}>
          發表種子構想
        </Button>
        <ul className="mt-2 list-disc list-inside text-sm text-gray-700">
          {seeds.map((seed, index) => (
            <li key={index} className="flex justify-between items-center">
              <span>{seed}</span>
              <Button
                variant="outline"
                size="sm"
                onClick={() => deleteSeed(index)}
              >
                刪除
              </Button>
            </li>
          ))}
        </ul>
      </Card>

      {/* 創想階段發表區 */}
      <Card className="p-4 mb-4">
        <h2 className="font-semibold mb-2">發表第1階段：創想內容</h2>
        <select
          value={seedForIdea}
          onChange={(e) => setSeedForIdea(e.target.value)}
          className="w-full p-2 border rounded mb-2"
        >
          <option value="">選擇對應的種子構想（可選）</option>
          {seeds.map((seed, index) => (
            <option key={index} value={seed}>
              {seed}
            </option>
          ))}
        </select>
        <Textarea
          value={newIdea}
          onChange={(e) => setNewIdea(e.target.value)}
          placeholder="輸入創想具體內容"
        />
        <Input
          type="file"
          accept="image/*"
          onChange={(e) => {
            if (e.target.files && e.target.files[0]) {
              const reader = new FileReader();
              reader.onload = (event) => {
                setImage(event.target.result);
              };
              reader.readAsDataURL(e.target.files[0]);
            }
          }}
          className="mt-2"
        />
        {image && <img src={image} alt="上傳的圖片" className="mt-2 max-w-full" />}
        <Input
          value={price}
          onChange={(e) => setPrice(e.target.value)}
          placeholder="設定買斷價格 (選填)"
          type="number"
          className="mt-2"
        />
        <Button className="mt-2" onClick={submitIdea}>
          發表創想
        </Button>
      </Card>

      {/* 創想列表 */}
      {filteredIdeas.map((idea, index) => (
        <Card key={index} className="p-4 mb-4">
          <CardContent>
            {idea.seed && (
              <p className="text-sm text-gray-600 italic">🌱 種子構想：{idea.seed}</p>
            )}
            <p className="mt-1 whitespace-pre-wrap">{idea.text}</p>
            {idea.image && <img src={idea.image} alt="創想圖片" className="mt-2 max-w-full" />}
            {idea.price && <p className="text-sm text-gray-500">價格: ${idea.price}</p>}
            <Button className="mt-2 mr-2" onClick={() => toggleLikeIdea(index)}>
              {likedIdeas[index] ? "取消讚" : "👍"} {idea.likes}
            </Button>
            <Button className="mt-2" onClick={() => toggleFavorite(index)}>
              {idea.isFavorited ? "💖 已收藏" : "🤍 收藏"}
            </Button>
            <div className="mt-2">
              <Input
                placeholder="留言..."
                onKeyDown={(e) => {
                  if (e.key === "Enter" && e.target.value.trim()) {
                    addComment(index, e.target.value);
                    e.target.value = "";
                  }
                }}
              />
            </div>
            <ul className="mt-2 text-sm text-gray-700">
              {idea.comments.map((comment, i) => (
                <li key={i}>💬 {comment}</li>
              ))}
            </ul>
          </CardContent>
        </Card>
      ))}
    </div>
  );
}
