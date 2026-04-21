Let me go deep on each one.

---

# 1. CourtSide

## What It Actually Is

A mobile/web app that generates live professional sports commentary for any amateur game happening in front of you. You open the app, tell it what sport is being played and the players' names, point your camera at the game, and it watches and generates real-time spoken commentary — complete with crowd noise, excitement, and the players' actual names being called out.

## The Real Problem It Solves

Amateur and youth sports are played in silence. A 10-year-old scores the best goal of their life and the only sound is parents clapping awkwardly. There is no atmosphere, no occasion, no sense that this moment matters. Professional sports feel epic because of commentary and sound design. Amateur sports feel small because of the absence of it. This is not a niche problem — hundreds of millions of children play organised sport globally and every single game happens without commentary.

## Who Uses It

Parents at youth sports events. Coaches who want to create highlight reels. Amateur leagues that want to elevate the experience. Schools on sports day. Five-a-side adult leagues that want the feeling of a real match.

## How It Uses ElevenLabs

**Voice Design API** — creates a unique commentator voice. Not a generic TTS voice. A voice designed to sound like a specific broadcaster — energetic, authoritative, warm. You can tune it: "sounds like a British football commentator, male, mid-40s, enthusiastic but measured."

**TTS v3 with emotion tags** — the commentary uses emotion tags dynamically. A near-miss gets `[tense]`. A goal gets `[excited, loud]`. A foul gets `[concerned]`. The same sentence spoken differently depending on what just happened.

**Sound Effects API** — crowd ambience plays underneath constantly, modulating in volume based on game intensity. A goal triggers a crowd roar. A free kick triggers quiet anticipation. These are generated and triggered in real time from game events.

**Music API** — pre-game warmup music, half-time interlude, post-game summary music. Gives the whole experience a broadcast feel.

## How It Uses Kiro

The Kiro spec defines the entire commentary logic system:

- **When to speak** — not every moment needs commentary. The spec defines triggers: goal, near-miss, foul, good pass, possession change, final minute.
- **How to speak** — tone rules for different game states. Losing team gets "fighting spirit" framing. Winning team gets "clinical finishing" framing.
- **Player consistency** — the spec maintains a roster and ensures the same player is always referred to the same way across the whole game.
- **Sport-specific rules** — football commentary is different from basketball commentary. The spec encodes the rules of the sport so commentary is accurate.
- **Pacing** — the spec defines silence as intentional. It knows when *not* to speak, which makes the moments it does speak feel earned.

Without the Kiro spec, the commentary is random text generation. With it, it behaves like a real broadcaster who knows the game.

## The Technical Architecture

1. Camera feed → computer vision model detects game events (ball position, player movement, goal detection)
2. Event classification → "goal scored by player 7" → feeds into commentary engine
3. Commentary engine (governed by Kiro spec) → generates spoken text with emotion tags
4. ElevenLabs TTS → speaks the commentary in real time
5. ElevenLabs SFX → triggers crowd sounds synchronized to game events
6. Audio mixing layer → blends commentary voice + crowd + ambient music

## The Demo

You film a real youth football game. A child dribbles past two players. The commentator voice says with genuine excitement: *"Brilliant run from Arjun — he's taken on two defenders — shoots — AND HE SCORES!"* Crowd noise erupts. The parent filming is shaking. That 15-second clip is the submission video. It gets shared by every parent in the group chat before the hackathon ends.

## Real World Potential

Charge per game session or subscription. Partner with youth sports leagues and school sports organisations. Sell to amateur tournament organisers. The long-term product adds instant highlight reel generation — the best 60 seconds of commentary automatically clipped and shareable on Instagram.

## The Hard Part (Exciting)

Real-time computer vision detecting game events accurately enough to trigger coherent commentary. The solution is to start with manual triggering — a parent taps a button when a goal is scored — and use vision as a secondary confirmation layer. This is actually more reliable for the demo and can be automated later.

---

# 2. PrepCall

## What It Actually Is

An app where you describe a difficult real conversation you need to have — firing someone, asking for a raise, confronting a parent, ending a relationship, pitching an investor — and the app creates a voice agent that plays the other person, based on everything you tell it about them. You have the conversation out loud, repeatedly, until you feel ready.

## The Real Problem It Solves

Everyone has a conversation they are avoiding right now. The standard advice is "practice what you want to say" but practicing alone in the mirror is useless — the other person never responds. Therapy is expensive and slow. Asking a friend to role-play is awkward and they don't push back hard enough because they care about you. There is currently no way to practice a high-stakes conversation against a realistic simulation of the actual person.

## Who Uses It

Anyone facing a hard conversation — which is everyone, regularly. Specific high-value audiences: people preparing for performance reviews and salary negotiations, managers who need to have difficult conversations with reports, couples about to have a relationship-defining talk, founders preparing for investor meetings, people in therapy who want to practice between sessions.

## How It Uses ElevenLabs

**Voice Design API** — this is the irreplaceable piece. You describe the person: "my boss, Sarah, 45, British, direct and no-nonsense, gets slightly cold when she feels challenged, speaks quickly." The Voice Design API generates a voice that matches this description. You are not practicing against a generic AI voice. You are practicing against something that sounds and feels like the actual person. This is psychologically important — a generic voice doesn't trigger the same anxiety as the real person, so you don't build real resistance.

**Conversational AI (ElevenAgents)** — the real-time conversation engine. It stays in character as the person you described, responds the way you told it they would respond, escalates when you said they escalate, softens when you find the right words.

**TTS v3 with emotion tags** — the character's emotional state changes based on how the conversation is going. Push too hard and they get `[cold, clipped]`. Find common ground and they become `[warmer, engaged]`. This is what makes it feel real rather than scripted.

**STT (Scribe)** — captures your spoken words accurately, including hesitations, which the app can reflect back to you as coaching.

## How It Uses Kiro

The Kiro spec is the character definition document. It contains:

- **Personality rules** — how this person communicates, what they respond well to, what makes them defensive
- **Known positions** — what the app knows about their likely starting position on the topic
- **Conversation arc** — how the conversation typically escalates or de-escalates with this person
- **Red lines** — things that will definitely end the conversation badly
- **Success criteria** — what "winning" this conversation looks like

The spec makes the agent consistent across multiple practice sessions. Without it, the agent gives different responses each time with no coherent personality. With it, you are practicing against a stable, believable character that behaves the same way your actual boss behaves.

## The Demo

Someone sits down and says: "I need to practice asking my manager for a 20% raise. She's called Jennifer. She's fair but she always asks about market data and she shuts down if she feels like I'm making it personal." The app builds Jennifer's voice and character in 10 seconds. The practice conversation starts. Jennifer asks: "What's driving this request?" The user stumbles. Jennifer gets slightly cooler. The user tries again. This time they lead with market data. Jennifer's voice warms slightly. The judge watching this immediately thinks of three conversations they have been avoiding.

## Real World Potential

Consumer subscription at £15/month. Enterprise version for sales training, management development, and leadership coaching. Integration with executive coaching platforms. The enterprise version alone is a serious B2B product — companies pay thousands for communication training that is far less effective than this.

---

# 3. SleepCast

## What It Actually Is

Every night before bed, you speak for 60–90 seconds about your day — whatever is on your mind. The app transforms what you said into a personalised bedtime audio story, narrated in a soothing voice, where the events of your actual day are gently reframed as a narrative arc. You drift off listening to your own life turned into a story with resolution.

## The Real Problem It Solves

People lie awake at night because they cannot process the day. The mind replays unresolved conversations, unfinished tasks, anxieties about tomorrow. Meditation apps tell you to breathe and let go, which does not work for people who are wired. Sleep stories on Calm or Headspace are beautiful but completely disconnected from what is actually bothering you. Nothing currently bridges what happened to you today and the story that helps you sleep tonight.

## Who Uses It

Anyone who has trouble switching off at night — which is an enormous market. Specific audiences: people with high-stress jobs, parents whose minds race after the kids are in bed, people going through a difficult period who need nightly processing, anyone who finds generic meditation ineffective.

## How It Uses ElevenLabs

**STT (Scribe)** — captures your spoken day recap accurately.

**TTS v3 with emotion tags** — narrates the story with genuine warmth and calm. `[softly]`, `[gently]`, `[warmly]` tags make the narration feel like a real storyteller, not a robot reading text. The voice slows down as the story approaches its resolution.

**Music API** — generates a custom ambient musical bed for tonight's story, matching the emotional tone of what you described. A stressful day gets slow, resolving harmonic progressions. A good day gets something slightly warmer and brighter.

**Sound Effects API** — subtle ambient layers. Rain, a distant fire, soft wind. Not generic — matched to the setting of your story. If you mentioned being in a coffee shop today, the story might be set in a warm café with distant background sounds.

## How It Uses Kiro

The Kiro spec is the narrative transformation engine. It contains:

- **Reframing rules** — how to transform real events into story form. A difficult meeting becomes "our protagonist faced a challenge that tested their patience." An unfinished task becomes "there was one thread left to pick up tomorrow, and it would be there waiting."
- **Tone arc** — every story must move from complexity toward resolution. The spec enforces this arc regardless of how bad the day was.
- **Character consistency** — people you mention frequently (partner, boss, friend) accumulate personality across nights so the story feels continuous.
- **Sleep-optimised pacing** — the spec defines slowing sentence length, vocabulary simplification, and increased silence as the story progresses. Engineered to induce sleep, not just entertain.
- **Emotional safety rules** — the spec prevents the story from dwelling on negative emotions or recreating anxiety. Everything gets reframed constructively.

## The Demo

Someone speaks: "Really hard day. My presentation went badly, my colleague undermined me in the meeting, and I snapped at my partner when I got home. Can't stop thinking about it." 20 seconds later, a warm, slow voice begins: *"Tonight's story begins at the end of a long day, where our traveller had carried many things across a difficult terrain... The meeting had been a storm — unexpected, loud, over quickly. But storms pass. What remained was the clarity they leave behind..."* The music is soft and resolving. By the third minute the judge is genuinely calmer.

## Real World Potential

Direct competitor to Calm and Headspace but with a fundamentally different and more personal mechanism. Consumer subscription. Integration with wearables that detect sleep quality and feed back into the narrative approach. Long-term: the app builds a private narrative diary of your life told as bedtime stories.

---

# 4. GriefVoice

## What It Actually Is

An app that helps families preserve a loved one's voice and presence before they are gone — or to reconnect with someone after loss. Family members contribute voice recordings, written memories, stories, and personality descriptions. The app creates a conversational voice presence that speaks in that person's voice, knows their stories, and can be talked to by family members who miss them.

## The Real Problem It Solves

People lose loved ones and are left with voicemails they replay obsessively, videos they cannot bring themselves to watch, and a silence where a person used to be. The pain is not just grief — it is the inability to ask one more question, share one more piece of news, hear one more piece of advice. Separately, families with elderly relatives or ill family members watch those people fade without capturing who they were while they still can.

## Who Uses It

Families with elderly relatives who want to preserve them while they are still present. People in the early stages of grief who want to maintain a connection. Adult children who want their own children to know a grandparent they never met.

## How It Uses ElevenLabs

**Voice Cloning** — the core. From recordings contributed by the family — voicemails, videos, old recordings — ElevenLabs builds a voice model. This is not a perfect replica. It is a deeply familiar voice that carries the texture and character of the person.

**Conversational AI (ElevenAgents)** — the agent that powers real-time conversation. It speaks in the cloned voice and draws on everything the family has contributed: stories, opinions, favourite phrases, how they responded to things.

**TTS v3 with emotion tags** — the voice carries emotional warmth appropriate to the conversation. A grandchild asking about a childhood story gets `[warm, nostalgic]`. A question about a hard time in their life gets `[reflective, honest]`.

## How It Uses Kiro

The Kiro spec is the person's character document. Built from family contributions, it contains:

- **Core personality traits** — how they spoke, their sense of humour, their values
- **Known stories** — specific memories and anecdotes they told frequently
- **Opinion positions** — what they believed about important things
- **Favourite expressions** — phrases they used that family members would recognise
- **Boundaries** — topics the family have decided the agent should not speak about or should redirect

The spec is what separates this from a generic chatbot. Without it, the agent sounds like a voice on a body that knows nothing. With it, the agent sounds like the person because it carries their knowledge and character consistently.

## The Ethical Consideration

This idea has genuine power and genuine risk. The risk: families who are not ready to process grief might use this to avoid it. The product needs consent frameworks, designed limitations, and mental health guidance baked in. The demo must be handled carefully — the judges will feel the weight of it and will ask hard questions.

The framing that works: position this as a **memory preservation** tool, not a grief replacement tool. The primary use case is families recording elderly relatives while they are still alive and well. The secondary use case is gentle reconnection after loss, with clear guidelines about healthy use.

## The Demo

A grandmother records stories with her grandchildren using the app. Six months later, after she has passed, a grandchild opens the app and asks: "Nani, what was your favourite memory from when you were young?" The cloned voice responds with a story the grandmother told once, preserved in the spec. The judge watching this either cries or feels profoundly uncomfortable. Both reactions mean the idea has power.

## Real World Potential

This is a category that does not exist yet and will exist. HereAfter AI and StoryFile have explored adjacent territory but without ElevenLabs voice quality and without the structured spec approach. The product has genuine societal value. It also has serious IP, consent, and ethics infrastructure to build — which is actually a moat.

---

# 5. KidCast

## What It Actually Is

A daily personalised audio news show for children aged 5–12, narrated in their own parent's cloned voice, covering real world events explained at the right level for their age and interests. Every morning a fresh 5-minute episode is ready, sounding like their parent is explaining the world to them.

## The Real Problem It Solves

Children grow up either shielded from the news entirely or accidentally exposed to adult news that is frightening and incomprehensible. Parents want their children to be curious, informed, and empathetic about the world — but translating the news into child-appropriate language every morning takes time and skill most parents do not have. Meanwhile the child hears nothing, or hears something frightening on the radio.

## Who Uses It

Parents of children aged 5–12 who care about raising curious, world-aware kids. Schools looking for morning circle resources. Parents who travel frequently and want a way to stay present in their child's daily learning.

## How It Uses ElevenLabs

**Voice Cloning** — the parent records 2 minutes of natural speech during setup. The app clones their voice. Every morning's episode sounds like their parent is talking to them directly, even if the parent left for work at 6am.

**TTS v3 with emotion tags** — the narration uses warmth, curiosity, and gentle excitement appropriate to a parent explaining something fascinating to a child.

**Music API** — a consistent show theme music that the child comes to recognise as "their morning show." Familiar, warm, slightly playful.

**SFX API** — light sound effects that make the show feel produced. A chime when a new topic starts. Ambient sounds that match the story being told.

## How It Uses Kiro

The Kiro spec is the editorial policy for each child:

- **Age-appropriateness rules** — vocabulary constraints, concept complexity limits, how to frame difficult topics for a 6-year-old versus a 10-year-old
- **Interest profile** — the child likes dinosaurs, space, football. Stories about those topics get prioritised and used as analogies to explain unrelated news
- **Topic filters** — parents set which topics are off-limits or need special handling
- **Tone rules** — everything must end with curiosity or hope, never fear
- **Consistency** — the spec maintains a "known world" for the child so the show builds on previous episodes

## The Demo

A parent sets up the app in 3 minutes — records their voice, sets their child's age and interests. Next morning, the child hears their parent's voice: *"Good morning! So yesterday something really interesting happened in a country called Iceland — you know, the one with all the volcanoes we talked about last week — well, one of those volcanoes started..."* The child sits up. It sounds like their parent. It knows they like volcanoes. It connects to something they discussed before. The parent watches their child's face and pulls out their phone to film it.

## The Technical Challenge

News data pipeline — you need a reliable source of age-appropriate, accurate news content to transform. This is solvable with RSS feeds from trusted sources plus an LLM transformation layer governed by the Kiro spec. The complexity is worth it because this pipeline is the core product value.

## Real World Potential

Subscription at £8/month per family. School licensing. Could be the product that makes ElevenLabs voice cloning mainstream for consumer use because the use case is so obviously positive and the cloned voice is used to help rather than deceive.

---

# 6. VoiceTutor

## What It Actually Is

A subject tutor that teaches entirely through spoken conversation. You never read anything. You ask questions, the tutor explains, you respond, the tutor adapts. It is like having a patient private tutor available at any moment who never gets tired of your questions and always explains things in the way that works for you.

## The Real Problem It Solves

Reading is not how most people learn best, but almost all learning resources are text-based. Lectures work for some people but they are passive. Tutors work brilliantly but cost £50–100 per hour. AI chatbots can explain things but require reading, which defeats the purpose for audio learners. There is no tool that teaches through pure conversation at the quality of a good human tutor.

## Who Uses It

Students at every level — school, university, professional qualification. Adult learners studying for certifications. People who want to understand a topic quickly without reading a textbook. Language learners who need conversational practice with real feedback.

## How It Uses ElevenLabs

**Voice Design API** — creates a tutor persona. Not a generic voice. A voice designed to carry patience, warmth, and intellectual engagement. You could have different tutors for different subjects with different voice characters — a calm measured voice for maths, an enthusiastic energetic voice for history.

**Conversational AI (ElevenAgents)** — the real-time tutoring engine. The tutor listens to your answer, detects whether you understood or are confused, and adjusts the next explanation accordingly.

**TTS v3 with emotion tags** — the tutor uses `[encouraging]` when you get something right, `[patient]` when you struggle, `[curious]` when introducing a new concept. This is emotionally intelligent tutoring, not robotic explanation delivery.

**STT (Scribe)** — captures your spoken answers with high accuracy, including when you trail off mid-sentence showing you are not sure, which the tutor notices.

## How It Uses Kiro

The Kiro spec is the curriculum document:

- **Learning objectives** — what the student must be able to explain, not just recall
- **Prerequisite chains** — you cannot move to calculus until you can demonstrate algebra. The spec enforces this
- **Assessment rules** — how to determine whether understanding has been achieved versus surface memorisation
- **Explanation strategies** — multiple ways to explain the same concept for different learning styles
- **Pacing rules** — how long to spend on a concept before moving on, when to review

The spec is what makes this a real tutor rather than a conversation partner. Without structured curriculum logic, the agent just chats about the subject. With the spec, it actually teaches.

## The Demo

Someone says "I want to understand how compound interest works." The tutor voice responds warmly: *"Great — let's start with something you definitely understand already. If I lend you £100 and you pay me back £110, what did that extra £10 represent?"* The student answers. The tutor builds on exactly what they said. In 4 minutes the student understands compound interest. They did not read a single word. The judge thinks about every concept they tried to learn from a textbook and gave up on.

## Real World Potential

The private tutoring market is enormous globally. Every student preparing for exams is a potential user. This competes directly with Khanmigo but with voice as the primary interface, which is a genuine differentiator. Subscription model, potentially with subject-specific packs.

---

# 7. StandupBot

## What It Actually Is

A tool that replaces the daily standup meeting. Each team member records a 60-second voice note on their phone. StandupBot transcribes all of them, identifies blockers, spots dependencies between people's work, and generates a single clean audio briefing that the whole team can listen to in 90 seconds while making coffee. No meeting. No scheduling. No one has to perform being awake at 9am.

## The Real Problem It Solves

The daily standup is the most universally disliked meeting in software teams. It was designed for co-located teams working in sprints. In 2026 it is mostly a synchronisation overhead that interrupts deep work and produces no output that could not have been achieved asynchronously. Teams keep doing it because there is no good alternative that provides the same team awareness. This is that alternative.

## Who Uses It

Remote and hybrid software teams. Any team doing daily standups who would prefer not to. Product managers who need to know what is blocked. Engineering leads who want visibility without meetings.

## How It Uses ElevenLabs

**STT (Scribe)** — transcribes each team member's voice note with speaker identification and high accuracy.

**TTS v3** — generates the synthesised team briefing in a consistent, professional voice. This is not robotic — it sounds like a clear-voiced colleague summarising the team's day.

**ElevenAgents** — the intelligent layer that identifies blockers from natural speech ("I'm waiting on the design file before I can finish this"), matches dependencies between team members' updates, and decides what is worth including in the briefing versus what is routine.

## How It Uses Kiro

The Kiro spec defines the standup intelligence layer:

- **What counts as a blocker** — specific language patterns and situations that indicate something is blocked
- **Dependency detection rules** — if person A is waiting for person B, the spec ensures both are notified in the briefing
- **Prioritisation rules** — what gets mentioned in the briefing and in what order
- **Team norms** — some teams care about mood and energy, others only care about blockers. The spec encodes the team's preferences
- **Escalation rules** — when something has been blocked for more than two days, the spec triggers an escalation flag in the briefing

## The Demo

Five voice recordings come in over 30 minutes in the morning. At 9:30am, a clean voice plays through the team's Slack channel: *"Good morning. Here's today's team update. Three people are on track. Priya is blocked waiting for the API documentation from Rahul — Rahul, heads up on that. The deployment from yesterday is confirmed stable. One risk to flag: the feature estimate from yesterday has expanded..."* No meeting happened. The whole team is aligned. The judge thinks about the last standup they sat through and felt nothing.

## Real World Potential

B2B SaaS with strong integration story — Slack, Linear, GitHub, Jira. Pricing per team member per month. The standup replacement market is real and underserved. This could be a standalone product or acquired by a project management tool.

---

# 8. AudioDiff

## What It Actually Is

A tool that monitors documents, codebases, or any content source and when something changes, reads that change aloud in plain conversational English — personalised to who is listening. A developer hears about code changes relevant to their work. A product manager hears about feature changes. A designer hears about UI changes. Nobody reads the diff. They just hear what changed and why it matters to them.

## The Real Problem It Solves

Important changes get missed because nobody reads changelogs, pull request descriptions, or document diffs. These are walls of text in formats designed for version control systems, not humans. The result is that team members are regularly surprised by changes that affected their work — because the notification existed but nobody engaged with it. If it were spoken to you in plain English while you made coffee, you would hear it.

## Who Uses It

Software development teams who miss important changes. Product managers who need to track what engineers are building. Legal and compliance teams who need to notice when policy documents change. Anyone managing a complex document environment where changes have consequences.

## How It Uses ElevenLabs

**TTS v3** — narrates the change summary in a natural, conversational voice. Not reading the diff. Explaining it, the way a knowledgeable colleague would explain it in a Slack message.

**Voice Design** — different notification types have different voice characters. Urgent security changes get a clear, direct voice. Routine updates get a calm, informational voice. This creates an audio hierarchy that listeners learn to interpret without thinking.

**ElevenAgents** — the intelligent summarisation and personalisation layer. The agent understands who is listening and what aspects of the change are relevant to them specifically.

## How It Uses Kiro

The Kiro spec defines the personalisation and relevance layer:

- **Role profiles** — what a developer cares about versus what a PM cares about versus what a designer cares about
- **Relevance rules** — which changes trigger a notification for which person
- **Summarisation depth** — a senior engineer gets more technical detail, a stakeholder gets business impact only
- **Urgency classification** — what is routine, what is notable, what is urgent
- **Relationship mapping** — if this change touches the API that person B owns, person B should hear about it even if they did not touch it

## The Demo

A developer merges a pull request. 15 seconds later, through the product manager's Slack: a voice says *"Quick heads up — the checkout flow just changed. The payment step now adds a confirmation screen before charging. The copy on that screen is placeholder right now — you'll want to review it before it goes to production."* The PM did not read a PR description. They did not check GitHub. They just heard it. The judge thinks about every change that went to production without them knowing.

## Real World Potential

Developer tooling market. GitHub integration is the entry point. Expands to any document change environment — Notion, Confluence, Google Docs. The product gets more valuable as the team grows because the volume of missed changes grows with team size. Enterprise pricing is strong because the cost of missed changes is quantifiable.

---

## Summary

Every one of these is a real product that exists independently of the hackathon. ElevenLabs is not decoration in any of them — remove it and the product does not work. Kiro's spec is not decoration in any of them — remove it and the product loses its intelligence and consistency.

The three that have the most devastating demos in a short video are **CourtSide**, **PrepCall**, and **SleepCast** — in that order. The three with the strongest business cases are **PrepCall**, **StandupBot**, and **AudioDiff**. The one with the highest emotional stakes is **GriefVoice**.

What matters to your team — demo power, business case, or emotional impact?
